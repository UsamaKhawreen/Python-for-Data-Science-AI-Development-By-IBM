<center>
    <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0101EN-SkillsNetwork/IDSNlogo.png" width="300" alt="cognitiveclass.ai logo"  />
</center>

# Watson Speech to Text Translator

Estimated time needed: **25** minutes

## Objectives

After completing this lab you will be able to:

*   Operate a Speech to Text Translator through an API


### Introduction

<p>In this notebook, you will learn to convert an audio file of an English speaker to text using a Speech to Text API. Then, you will translate the English version to a Spanish version using a Language Translator API. <b>Note:</b> You must obtain the API keys and endpoints to complete the lab.</p>


<div class="alert alert-block alert-info" style="margin-top: 20px">
<h2>Table of Contents</h2>
<ul>
    <li><a href="https://#ref0">Speech To Text</a></li>
    <li><a href="https://#ref1">Language Translator</a></li>
    <li><a href="https://#ref2">Exercise</a></li>
</ul>
</div>



```python
#you will need the following library 
!pip install ibm_watson wget
```

<h2 id="ref0">Speech to Text</h2>


<p>First we import <code>SpeechToTextV1</code> from <code>ibm_watson</code>. For more information on the API, please click on this <a href="https://cloud.ibm.com/apidocs/speech-to-text?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMDeveloperSkillsNetworkPY0101ENSkillsNetwork19487395-2022-01-01&code=python">link</a>.</p>



```python
from ibm_watson import SpeechToTextV1 
import json
from ibm_cloud_sdk_core.authenticators import IAMAuthenticator
```

<p>The service endpoint is based on the location of the service instance, we store the information in the variable URL. To find out which URL to use, view the service credentials and paste the url here.</p>



```python
url_s2t = ""
```

<p>You require an API key, and you can obtain the key on the <a href="https://cloud.ibm.com/resources?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMDeveloperSkillsNetworkPY0101ENSkillsNetwork19487395-2022-01-01">Dashboard </a>.</p>



```python
iam_apikey_s2t = ""
```

<p>You create a <a href="http://watson-developer-cloud.github.io/python-sdk/v0.25.0/apis/watson_developer_cloud.speech_to_text_v1.html?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMDeveloperSkillsNetworkPY0101ENSkillsNetwork19487395-2022-01-01">Speech To Text Adapter object</a> the parameters are the endpoint and API key.</p>



```python
authenticator = IAMAuthenticator(iam_apikey_s2t)
s2t = SpeechToTextV1(authenticator=authenticator)
s2t.set_service_url(url_s2t)
s2t
```

<p>Lets download the audio file that we will use to convert into text.</p>



```python
!wget -O PolynomialRegressionandPipelines.mp3  https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0101EN-SkillsNetwork/labs/Module%205/data/PolynomialRegressionandPipelines.mp3


```

<p>We have the path of the .wav file we would like to convert to text</p>



```python
filename='PolynomialRegressionandPipelines.mp3'
```

<p>We create the file object <code>wav</code> with the wav file using <code>open</code>. We set the <code>mode</code> to  "rb" ,  this is similar to read mode, but it ensures the file is in binary mode. We use the method <code>recognize</code> to return the recognized text. The parameter <code>audio</code> is the file object <code>wav</code>, the parameter <code>content_type</code> is the format of the audio file.</p>



```python
with open(filename, mode="rb")  as wav:
    response = s2t.recognize(audio=wav, content_type='audio/mp3')
```

<p>The attribute result contains a dictionary that includes the translation:</p>



```python
response.result
```


```python
from pandas import json_normalize

json_normalize(response.result['results'],"alternatives")
```


```python
response
```

<p>We can obtain the recognized text and assign it to the variable <code>recognized_text</code>:</p>



```python
recognized_text=response.result['results'][0]["alternatives"][0]["transcript"]
type(recognized_text)
```

<h2 id="ref1">Language Translator</h2>


<p>First we import <code>LanguageTranslatorV3</code> from ibm_watson. For more information on the API click <a href="https://cloud.ibm.com/apidocs/speech-to-text?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMDeveloperSkillsNetworkPY0101ENSkillsNetwork19487395-2022-01-01&code=python"> here</a></p>



```python
from ibm_watson import LanguageTranslatorV3
```

<p>The service endpoint is based on the location of the service instance, we store the information in the variable URL. To find out which URL to use, view the service credentials.</p>



```python
url_lt=''
```

<p>You require an API key, and you can obtain the key on the <a href="https://cloud.ibm.com/resources?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMDeveloperSkillsNetworkPY0101ENSkillsNetwork19487395-2022-01-01">Dashboard</a>.</p>



```python
apikey_lt=''
```

<p>API requests require a version parameter that takes a date in the format version=YYYY-MM-DD. This lab describes the current version of Language Translator, 2018-05-01</p>



```python
version_lt='2018-05-01'
```

<p>we create a  Language Translator object <code>language_translator</code>:</p>



```python
authenticator = IAMAuthenticator(apikey_lt)
language_translator = LanguageTranslatorV3(version=version_lt,authenticator=authenticator)
language_translator.set_service_url(url_lt)
language_translator
```

<p>We can get a Lists the languages that the service can identify.
The method Returns the language code.  For example English (en) to  Spanis (es) and name of each language.</p>



```python
from pandas import json_normalize

json_normalize(language_translator.list_identifiable_languages().get_result(), "languages")
```

<p>We can use the method <code>translate</code>. This will translate the text. The parameter text is the text, Model_id is the type of model we would like to use use we use list the language. In this case, we set it to 'en-es' or English to Spanish. We get a Detailed Response object translation_response</p>



```python
translation_response = language_translator.translate(\
    text=recognized_text, model_id='en-es')
translation_response
```

<p>The result is a dictionary.</p>



```python
translation=translation_response.get_result()
translation
```

<p>We can obtain the actual translation as a string as follows:</p>



```python
spanish_translation =translation['translations'][0]['translation']
spanish_translation 
```

<p>We can translate back to English</p>



```python
translation_new = language_translator.translate(text=spanish_translation ,model_id='es-en').get_result()
```

<p>We can obtain the actual translation as a string as follows:</p>



```python
translation_eng=translation_new['translations'][0]['translation']
translation_eng
```

<br>


<h2>Quiz</h2>


Translate to French.



```python
# Write your code below and press Shift+Enter to execute

```

<details><summary>Click here for the solution</summary>

```python
French_translation=language_translator.translate(
    text=translation_eng , model_id='en-fr').get_result()

French_translation['translations'][0]['translation']

```

</details>


<h3>Language Translator</h3>


<b>References</b>


[https://cloud.ibm.com/apidocs/speech-to-text?code=python](https://cloud.ibm.com/apidocs/speech-to-text?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMDeveloperSkillsNetworkPY0101ENSkillsNetwork19487395-2022-01-01&code=python)


[https://cloud.ibm.com/apidocs/language-translator?code=python](https://cloud.ibm.com/apidocs/language-translator?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMDeveloperSkillsNetworkPY0101ENSkillsNetwork19487395-2022-01-01&code=python)


<hr>


## Authors:

[Joseph Santarcangelo](https://www.linkedin.com/in/joseph-s-50398b136/?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMDeveloperSkillsNetworkPY0101ENSkillsNetwork19487395-2022-01-01)

Joseph Santarcangelo has a PhD in Electrical Engineering, his research focused on using machine learning, signal processing, and computer vision to determine how videos impact human cognition. Joseph has been working for IBM since he completed his PhD.

## Other Contributor(s)

<a href="https://www.linkedin.com/in/fanjiang0619/?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMDeveloperSkillsNetworkPY0101ENSkillsNetwork19487395-2022-01-01">Fan Jiang</a>

## Change Log

| Date (YYYY-MM-DD) | Version | Changed By | Change Description                 |
| ----------------- | ------- | ---------- | ---------------------------------- |
| 2021-04-07        | 2.2     | Malika     | Updated the libraries              |
| 2021-01-05        | 2.1     | Malika     | Added a library                    |
| 2020-08-26        | 2.0     | Lavanya    | Moved lab to course repo in GitLab |
|                   |         |            |                                    |
|                   |         |            |                                    |

<hr/>

## <h3 align="center"> © IBM Corporation 2020. All rights reserved. <h3/>

