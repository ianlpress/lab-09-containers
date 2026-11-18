# Lab 09: Knowledge check

**Answer each question below it, in your own words, in one to three sentences.** Replace the `...` below **Your answer:** with your text.

## 1. Credentials in the image?

Assume you want to share your image with others and make it publicly available on `ghcr.io`, so that anyone can pull it and run the analysis. A teammate suggests adding `COPY .aws /home/app/.aws` to the `Dockerfile`, so that nobody has to type `-v ~/.aws:/home/app/.aws:ro` any more. (This is a thought experiment: keep your own package private.)

**Your answer:** Give two reasons why this is a bad idea. Think of who could then read your credentials, and of what happens when your Learner Lab session ends.

If the image is public, anyone could pull it and grab my AWS keys out of it, even if I deleted them in a later step. Also, the Learner Lab keys change every session, so the ones in the image would just stop working.

## 2. Where did the Atlas password come from?

In Step 4, you ran the analysis once with `-v ~/.aws:/home/app/.aws:ro` only, without the three `-e` options. The container still wrote to your Atlas cluster.

*Hint:* Read `get_atlas_settings()` in `src/book_analysis/analyze.py`.

**Your answer:** Explain, step by step, where the container found the Atlas settings in that run.

Since I didn't pass the Atlas variables, the code didn't find `MONGODB_ATLAS_URL` and fell back to the SSM parameter `mongodb_credentials`. It could read that because `boto3` found my mounted AWS credentials, and the JSON in there had the url, user, and password.

## 3. Same tag, different image

Run `docker images book-analysis`, and note the image ID of `book-analysis:0.1.0`. Then change the `description` in the `LABEL` of your `Dockerfile`, build again with the same command, `docker build -t book-analysis:0.1.0 .`, and run `docker images book-analysis` again.

**Your answer:** Paste the image ID from before and after the rebuild. Why is it a problem for someone who uses your image that `0.1.0` now names a different image, and what should you have done instead?

Image ID before: `70390ecee2d2`
Image ID after: `a9b778995679`

A tag is just a name, so now `0.1.0` points at a different image, and anyone using it could get something different than before. I should've bumped the version to `0.1.1` instead of rebuilding under the same tag.
