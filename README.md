## Overview
Google Cloud Vision API has empowered us with visual detection in miscellaneous applications. Its function ranges from detecting handwriting to people’s faces. Some professions, like a police officer, utilize Google Vision API on the street to capture information from the street such as road signs, license plates, etc. However, it is hard to imagine that any of these scenarios enables the Vision API to take a close look at the image from a perfect angle. Can Google Cloud Vision API still read as well if the texts in the picture are blurry or tilted? We are intrigued by whether angle would affect the accuracy of the text detection in Cloud Vision API.
![image](https://user-images.githubusercontent.com/42985861/116646600-7b843980-a946-11eb-988e-b754d7d686b8.png)

## How it works
With the question in our mind, we employed a quantitative method to gather data on the Vision API. In order to have the object being detected, we printed out some random letters and numbers in the font of 48 on a piece of paper. Subsequently, we take pictures of the printed texts with the disparity in angles while holding the distance from camera to the center of text controlled. Each picture differs by 15 degrees. Photos with different angles empower us to recreate the scenarios in real life when the texts are not at a perfectly readable angle. Then we requested Google Cloud Vision API into Google Colab and scanned each photo with the API using the following code.
![image](https://user-images.githubusercontent.com/42985861/116646644-948cea80-a946-11eb-99d2-d4abd3372d5d.png)
![image](https://user-images.githubusercontent.com/42985861/116646999-8095b880-a947-11eb-9ef4-dd7cb5c26453.png | width = 50)


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

Code source link: https://colab.research.google.com/drive/1OHGs7vDWc8B_HzxR9DukuTtxS158XGJv?usp=sharing

Actual notebook for coding: https://colab.research.google.com/drive/1hHpnBUnL5IKf6ypSMTXDojJf1w0q2xN2?usp=sharing

We ran the code with different images uploaded and recorded results from each trial. After recording all results from the Colab notebook, we came up with a spreadsheet with the accuracies of each trial.

## Data collection and Visualization
![image](https://user-images.githubusercontent.com/42985861/116646673-a9697e00-a946-11eb-9691-e2ea88df75a8.png)
![image](https://user-images.githubusercontent.com/42985861/116646704-c00fd500-a946-11eb-9f11-2a257cc38cd2.png)
![image](https://user-images.githubusercontent.com/42985861/116646736-d4ec6880-a946-11eb-8d62-61cbb94a4e87.png)

link to spreedsheet: https://docs.google.com/spreadsheets/d/1SJ4b9VI2UnBfqogqkjYnx_SPtqtdToTetwYh6Gk1rBM/edit?usp=sharing


## Analysis
We arranged the data collected into a table and then visualized them in Google Spreadsheets. It is obvious from the table that for all three texts (numbers, capital letters, a mix of numbers, and capital letters), the Vision API could recognize the texts 100% accurately for angles of 0, 15, 30, 45 degrees. For 60 degrees and 75 degrees, a certain level of decrease in accuracy took place for recognizing all three texts (0% - 40% for numbers, 0% - 100% for capital letters, 29% - 57% for the mix of numbers and capital letters). As for numbers, we noticed from the plot that at 60 degrees, the API reads the number “5” as the letter “s”. Whereas at 75 degrees, the Vision API could not read a single numerical character. Thus, we could infer that the increase in angles would gradually increase the difficulty for the Vision API to detect the texts.

## Conclusion
To sum up, we confirmed our idea that angle would affect the accuracy of the text detection in the Vision API. We took photos of printed random letters and numbers from different angles and summarized the result in the spreadsheet. From the table we made in the data studio, we found that all three types of texts (numbers, capital letters, a mix of numbers, and capital letters) show great accuracy under small angles. Nevertheless, 75 degrees is too large for the Vision API to recognize our texts, as a very low accuracy would be the result. The main difference occurs at 45 degrees, where numbers, capital letters, and a mix of numbers show variance in accuracy: capital letters > a mix of numbers and capital letters > numbers. The reason behind this may be that letters have clearer structures. We expect Google Cloud Vision API could improve its accuracy at reading texts from a larger angle to be more useful in real-life scenarios.
