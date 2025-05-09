# Mistral API

## Document OCR processor 

The Document OCR (Optical Character Recognition) processor, powered by our latest OCR model `mistral-ocr-latest`, enables you to extract text and structured content from PDF documents. 

**Key features**:
- Extracts text content while maintaining document structure and hierarchy
- Preserves formatting like headers, paragraphs, lists and tables
- Returns results in markdown format for easy parsing and rendering
- Handles complex layouts including multi-column text and mixed content
- Processes documents at scale with high accuracy
- Supports multiple document formats including PDF, images, and uploaded documents

The OCR processor returns both the extracted text content and metadata about the document structure, making it easy to work with the recognized content programmatically.

### OCR with PDF

```python
import os
from mistralai import Mistral

api_key = os.environ["MISTRAL_API_KEY"]
client = Mistral(api_key=api_key)

ocr_response = client.ocr.process(
    model="mistral-ocr-latest",
    document={
        "type": "document_url",
        "document_url": "https://arxiv.org/pdf/2201.04234"
    },
    include_image_base64=True
)
```

Or passing a Base64 encoded pdf:
```python
import base64
import requests
import os
from mistralai import Mistral

def encode_pdf(pdf_path):
    """Encode the pdf to base64."""
    try:
        with open(pdf_path, "rb") as pdf_file:
            return base64.b64encode(pdf_file.read()).decode('utf-8')
    except FileNotFoundError:
        print(f"Error: The file {pdf_path} was not found.")
        return None
    except Exception as e:  # Added general exception handling
        print(f"Error: {e}")
        return None

# Path to your pdf
pdf_path = "path_to_your_pdf.pdf"

# Getting the base64 string
base64_pdf = encode_pdf(pdf_path)

api_key = os.environ["MISTRAL_API_KEY"]
client = Mistral(api_key=api_key)

ocr_response = client.ocr.process(
    model="mistral-ocr-latest",
    document={
        "type": "document_url",
        "document_url": f"data:application/pdf;base64,{base64_pdf}" 
    }
)
```
### OCR with uploaded PDF

You can also upload a PDF file and get the OCR results from the uploaded PDF. 

#### Upload a file

```python
from mistralai import Mistral
import os

api_key = os.environ["MISTRAL_API_KEY"]

client = Mistral(api_key=api_key)

uploaded_pdf = client.files.upload(
    file={
        "file_name": "uploaded_file.pdf",
        "content": open("uploaded_file.pdf", "rb"),
    },
    purpose="ocr"
)  
```

#### Retrieve File

```python
retrieved_file = client.files.retrieve(file_id=uploaded_pdf.id)
```

Result:

```
id='00edaf84-95b0-45db-8f83-f71138491f23' object='file' size_bytes=3749788 created_at=1741023462 filename='uploaded_file.pdf' purpose='ocr' sample_type='ocr_input' source='upload' deleted=False num_lines=None
```

#### Get signed URL

```python
signed_url = client.files.get_signed_url(file_id=uploaded_pdf.id)
```

#### Get OCR results

```python
import os
from mistralai import Mistral

api_key = os.environ["MISTRAL_API_KEY"]
client = Mistral(api_key=api_key)

ocr_response = client.ocr.process(
    model="mistral-ocr-latest",
    document={
        "type": "document_url",
        "document_url": signed_url.url,
    }
)
```

### OCR with image

```python
import os
from mistralai import Mistral

api_key = os.environ["MISTRAL_API_KEY"]
client = Mistral(api_key=api_key)

ocr_response = client.ocr.process(
    model="mistral-ocr-latest",
    document={
        "type": "image_url",
        "image_url": "https://raw.githubusercontent.com/mistralai/cookbook/refs/heads/main/mistral/ocr/receipt.png"
    }
)
```

Or passing a Base64 encoded image:
```python
import base64
import requests
import os
from mistralai import Mistral

def encode_image(image_path):
    """Encode the image to base64."""
    try:
        with open(image_path, "rb") as image_file:
            return base64.b64encode(image_file.read()).decode('utf-8')
    except FileNotFoundError:
        print(f"Error: The file {image_path} was not found.")
        return None
    except Exception as e:  # Added general exception handling
        print(f"Error: {e}")
        return None

# Path to your image
image_path = "path_to_your_image.jpg"

# Getting the base64 string
base64_image = encode_image(image_path)

api_key = os.environ["MISTRAL_API_KEY"]
client = Mistral(api_key=api_key)

ocr_response = client.ocr.process(
    model="mistral-ocr-latest",
    document={
        "type": "image_url",
        "image_url": f"data:image/jpeg;base64,{base64_image}" 
    }
)
```

## Document understanding

The Document understanding capability combines OCR with large language model capabilities to enable natural language interaction with document content. This allows you to extract information and insights from documents by asking questions in natural language.

**The workflow consists of two main steps:**

1. Document Processing: OCR extracts text, structure, and formatting, creating a machine-readable version of the document.

2. Language Model Understanding: The extracted document content is analyzed by a large language model. You can ask questions or request information in natural language. The model understands context and relationships within the document and can provide relevant answers based on the document content.


**Key capabilities:**
- Question answering about specific document content
- Information extraction and summarization
- Document analysis and insights
- Multi-document queries and comparisons
- Context-aware responses that consider the full document

**Common use cases:**
- Analyzing research papers and technical documents
- Extracting information from business documents
- Processing legal documents and contracts
- Building document Q&A applications
- Automating document-based workflows

The examples below show how to interact with a PDF document using natural language:

```python
import os
from mistralai import Mistral

# Retrieve the API key from environment variables
api_key = os.environ["MISTRAL_API_KEY"]

# Specify model
model = "mistral-small-latest"

# Initialize the Mistral client
client = Mistral(api_key=api_key)

# If local document, upload and retrieve the signed url
# uploaded_pdf = client.files.upload(
#     file={
#         "file_name": "uploaded_file.pdf",
#         "content": open("uploaded_file.pdf", "rb"),
#     },
#     purpose="ocr"
# )
# signed_url = client.files.get_signed_url(file_id=uploaded_pdf.id)

# Define the messages for the chat
messages = [
    {
        "role": "user",
        "content": [
            {
                "type": "text",
                "text": "what is the last sentence in the document"
            },
            {
                "type": "document_url",
                "document_url": "https://arxiv.org/pdf/1805.04770"
                # "document_url": signed_url.url
            }
        ]
    }
]

# Get the chat response
chat_response = client.chat.complete(
    model=model,
    messages=messages
)

# Print the content of the response
print(chat_response.choices[0].message.content)

# Output: 
# The last sentence in the document is:\n\n\"Zaremba, W., Sutskever, I., and Vinyals, O. Recurrent neural network regularization. arXiv:1409.2329, 2014.
```

## Cookbooks
For more information and guides on how to make use of OCR and leverage document understanding, we have the following cookbooks:
- [Tool Use and Document Understanding](https://colab.research.google.com/github/mistralai/cookbook/blob/main/mistral/ocr/document_understanding.ipynb)
- [Batch OCR](https://colab.research.google.com/github/mistralai/cookbook/blob/main/mistral/ocr/batch_ocr.ipynb)
- [Structured OCR](https://colab.research.google.com/github/mistralai/cookbook/blob/main/mistral/ocr/structured_ocr.ipynb)

## FAQ
### Are there any limits regarding the OCR API?
Yes, there are certain limitations for the OCR API. Uploaded document files must not exceed 50 MB in size and should be no longer than 1,000 pages.
