# QTM250-HW4
This is a project intended to discover how accurate machine learning API can recognize photos of numbers and uppercase letters with different shooting angles.

## Code
The images to be tested are in the "image" folder, and the description to each image are in "ImageNaming" in Image folder. 
```
# Import necessary packages
import getpass
import requests
from googleapiclient.discovery import build
# Extract APIKey and save it
APIKEY = getpass.getpass()
```

```
# Import image url contain only number with a 0 degree shooting angle 
IMAGE="gs://web-page-edward/Num0.jpg" #Change the image to be tested here.

# Get cloud machine learning API service
vservice = build('vision', 'v1', developerKey=APIKEY)

# Detect the text with the google vision API
request = vservice.images().annotate(body={
        'requests': [{
                'image': {
                    'source': {
                        'gcs_image_uri': IMAGE
                    }
                },
                'features': [{
                    'type': 'TEXT_DETECTION',
                    'maxResults': 3,
                }]
            }],
        })
responses = request.execute(num_retries=3)
print(responses['responses'][0]['textAnnotations'][0]['description'])
```
