# Vertex Generative AI SDK for Python
The Vertex Generative AI SDK helps developers use Google's generative AI
[Gemini models](http://cloud.google.com/vertex-ai/docs/generative-ai/multimodal/overview)
and [PaLM language models](http://cloud.google.com/vertex-ai/docs/generative-ai/language-model-overview)
to build AI-powered features and applications.
The SDKs support use cases like the following:

- Generate text from texts, images and videos (multimodal generation)
- Build stateful multi-turn conversations (chat)
- Function calling

## Installation

To install the
[google-cloud-aiplatform](https://pypi.org/project/google-cloud-aiplatform/)
Python package, run the following command:

```shell
pip3 install --upgrade --user google-cloud-aiplatform
```

## Usage

For detailed instructions, see [Introduction to multimodal classes in the Vertex AI SDK](http://cloud.google.com/vertex-ai/docs/generative-ai/multimodal/sdk-for-gemini/gemini-sdk-overview-reference).

#### Imports:
```python
from vertexai.preview.generative_models import GenerativeModel, Image, Content, Part, Tool, FunctionDeclaration, GenerationConfig
```

#### Basic generation:
```python
from vertexai.preview.generative_models import GenerativeModel
model = GenerativeModel("gemini-pro")
print(model.generate_content("Why is sky blue?"))
```

#### Using images and videos
```python
from vertexai.preview.generative_models import GenerativeModel, Image
print(vision_model = GenerativeModel("gemini-pro-vision"))

# Local image
image = Image.load_from_file("image.jpg")
print(vision_model.generate_content(image))

# Image from Cloud Storage
image_part = generative_models.Part.from_uri("gs://download.tensorflow.org/example_images/320px-Felis_catus-cat_on_snow.jpg", mime_type="image/jpeg")
print(vision_model.generate_content(image_part))

# Text and image
print(vision_model.generate_content(["What is shown in this image?", image]))

# Text and video
video_part = Part.from_uri("gs://cloud-samples-data/video/animals.mp4", mime_type="video/mp4")
print(vision_model.generate_content(["What is in the video? ", video_part]))
```

#### Chat
```
from vertexai.preview.generative_models import GenerativeModel, Image
vision_model = GenerativeModel("gemini-ultra-vision")
vision_chat = vision_model.start_chat()
image = Image.load_from_file("image.jpg")
print(vision_chat.send_message(["I like this image.", image]))
print(vision_chat.send_message("What things do I like?."))
```

#### Function calling

```
# First, create tools that the model is can use to answer your questions.
# Describe a function by specifying it's schema (JsonSchema format)
get_current_weather_func = generative_models.FunctionDeclaration(
    name="get_current_weather",
    description="Get the current weather in a given location",
    parameters={
        "type": "object",
        "properties": {
            "location": {
                "type": "string",
                "description": "The city and state, e.g. San Francisco, CA"
            },
            "unit": {
                "type": "string",
                "enum": [
                    "celsius",
                    "fahrenheit",
                ]
            }
        },
        "required": [
            "location"
        ]
    },
)
# Tool is a collection of related functions
weather_tool = generative_models.Tool(
    function_declarations=[get_current_weather_func],
)

# Use tools in chat:
model = GenerativeModel(
    "gemini-pro",
    # You can specify tools when creating a model to avoid having to send them with every request.
    tools=[weather_tool],
)
chat = model.start_chat()
# Send a message to the model. The model will respond with a function call.
print(chat.send_message("What is the weather like in Boston?"))
# Then send a function response to the model. The model will use it to answer.
print(chat.send_message(
    Part.from_function_response(
        name="get_current_weather",
        response={
            "content": {"weather": "super nice"},
        }
    ),
))
```

## Documentation

You can find complete documentation for the Vertex AI SDKs and the Gemini model in the Google Cloud [documentation](https://cloud.google.com/vertex-ai/docs/generative-ai/learn/overview)

## Contributing

See [Contributing](https://github.com/googleapis/python-aiplatform/blob/main/CONTRIBUTING.rst) for more information on contributing to the Vertex AI Python SDK.

## License

The contents of this repository are licensed under the [Apache License, version 2.0](http://www.apache.org/licenses/LICENSE-2.0).
