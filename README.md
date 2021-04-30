#QTM250 Homework 4
## Overview
Google Cloud Vision API has empowered us with visual detection in miscellaneous applications. Its function ranges from detecting handwriting to people’s faces. Some professions, like a police officer, utilize Google Vision API on the street to capture information from the street such as road signs, license plates, etc. However, it is hard to imagine that any of these scenarios enables the Vision API to take a close look at the image from a perfect angle. Can Google Cloud Vision API still read as well if the texts in the picture are blurry or tilted? We are intrigued by whether angle would affect the accuracy of the text detection in Cloud Vision API.

## How it works
With the question in our mind, we employed a quantitative method to gather data on the Vision API. In order to have the object being detected, we printed out some random letters and numbers in the font of 48 on a piece of paper. Subsequently, we take pictures of the printed texts with the disparity in angles while holding the distance from camera to the center of text controlled. Each picture differs by 15 degrees. Photos with different angles empower us to recreate the scenarios in real life when the texts are not at a perfectly readable angle. Then we requested Google Cloud Vision API into Google Colab and scanned each photo with the API using the following code.
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
