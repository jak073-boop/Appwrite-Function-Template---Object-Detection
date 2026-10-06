# Appwrite-Function-Template---Object-Detection

Ported Hugging Face-based object detection/image classification function templates from Node.js to Python for Appwrite, an open-source backend-as-a-service platform with 75,000+ monthly active users.

Originally submitted as [appwrite/templates pull request #323](https://github.com/appwrite/templates/pull/323).

## What does this PR do?

**This PR adds a new template for "Object Detection with Hugging Face" which has Python and Node.js support now.**
*Down below is almost identical process for Image Classification with Hugging Face"
## The Main Package includes

### My Setup file

- Run `python setup.py` and it will automatically generate a database with a collection and its IDs, as well as a bucket so you can put image files in for testing
- This file is also used to check whether a database or bucket and the items within it already exist

### My AppwriteService file

- Loads environment variables from a `.env` file
- An Appwrite Service class is made, which the Setup file fetches
  - Has the actual functionality to produce Databases and Storage services for interacting with the local Appwrite server
- Initializes the `AppwriteService` class with an API key
- Sets up the Appwrite client with endpoint, project ID, and API key
- Defines a method `createImageLabels`
  - This creates a new space for results to be stored in the database
  - In this case, this is where the detected labels and bounding boxes are stored after a successful attempt

### My Main file

- The entry point for testing the integration between the object detection method from Hugging Face and my local Appwrite server
  - Requires my mock test to execute and produce results
- Initializes necessary services and starts the image processing
- This file ensures all necessary configurations and services are set up

### Added Utility file

- Ensures that all required keys are present in a given dictionary-like object
- Prevents runtime errors by validating the presence of necessary fields before proceeding
- Checks if each key is present in the object
- Collects any missing keys in a list
- Provides a simple and reusable way to validate input data
  - The main file uses the utility's `throw_if_missing` function to validate required fields before processing the image file

## Outside of the Main Process

- Added `.gitignore` file to ignore unnecessary files
- Added a `README.md` to document the usage, configuration, and environment variables needed
- Added requirements file
- Added `docker-compose.yml` (generated when the local Appwrite server runs smoothly with no issues)

## My Test Plan

1. **Install dependencies**

   ```bash
   pip install appwrite
   pip install huggingface_hub
   pip install python-dotenv
   ```

2. **Run Docker for Appwrite in a terminal within the parent directory of the template**

   ```bash
   cd python/object_detection_with_huggingface
   ```

   Source: https://appwrite.io/docs/advanced/self-hosting

   ```bash
   docker run -it --rm \
     --volume /var/run/docker.sock:/var/run/docker.sock \
     --volume "$(pwd)"/appwrite:/usr/src/code/appwrite:rw \
     --entrypoint="install" \
     appwrite/appwrite:1.6.0
   ```

   For replication purposes, make sure to use the default recommendations when prompted (port 80, port 443, localhost, etc.).

3. **After installation, use port 80 (for example) to sign up and create an account**

   Retrieve your project ID and key only.

4. **Environment setup**

   Objective: ensure all environment variables are correctly set.

   Verify the `.env` file contains:

   ```text
   APPWRITE_ENDPOINT=         (Navigate to settings in your project within the local Appwrite server)
   APPWRITE_API_KEY=          (Navigate to settings in your project within the local Appwrite server)
   APPWRITE_PROJECT_ID=       (Create a project after you sign in to your local server)
   HUGGINGFACE_ACCESS_TOKEN=  (Create your token: https://huggingface.co/docs/hub/en/security-tokens)
   APPWRITE_DATABASE_ID=      (Created when running setup.py)
   APPWRITE_COLLECTION_ID=    (Created when running setup.py)
   APPWRITE_BUCKET_ID=        (Created when running setup.py)
   APPWRITE_FILE_ID=          (After running setup.py: local server -> Storage -> bucket -> Add file -> upload -> retrieve ID)
   ```

5. **Use this request to execute `main.py` and get things running**

   **Mock test for this template (WIP)**

   ```python
   import asyncio
   import json


   class MockRequest:
       def __init__(self, method, body_json, headers):
           self.method = method
           self.body_json = body_json
           self.headers = headers


   class MockResponse:
       def json(self, data, status=200):
           data = json.dumps(data, indent=4)
           print(f"Response: {status}, Data: {data}")
           return data


   req = MockRequest(
       "POST",
       {"image": "your test file id", "bucketId": "object_detection"},
       {"x-appwrite-key": "your appwrite key"}
   )
   res = MockResponse()


   log = print
   error = print


   asyncio.run(process_object_detection(req, res, log, error))
   ```

## Test (Completed)

**My test image file:**

![image](https://github.com/user-attachments/assets/f5b58f01-a114-4a44-9063-e85eedf4dbad)

**Successful results:**

![image](https://github.com/user-attachments/assets/f3321c58-91ec-4b99-9789-df4dc9e179f7)

## Resources

Created a cited guide for navigating how to run Appwrite locally:
https://docs.google.com/document/d/1uPj4TdY5sdGFFG8uy-g47OhRXsYx1DoBchHDZu6cM2E/edit?usp=sharing