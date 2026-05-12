<div align="center">
    
# 📦 Chat Nio Blob Service

### **🤯 File Service for Chat Nio**

[![Deploy to Vercel](https://vercel.com/button)](https://vercel.com/new/clone?repository-url=https://github.com/Deeptrain-Community/chatnio-blob-service)
[![Deploy to PandaStack](https://dashboard.pandastack.io/deploy-button.svg)](https://dashboard.pandastack.io/deploy?repo=coaidev/blob-service&type=static&buildCmd=npm+run+build&outputDir=dist)

[![Deploy on Zeabur](https://zeabur.com/button.svg)](https://zeabur.com/templates/RWGFOH)

</div>

## Features
- ⚡ **Out-of-the-Box**: No External Dependencies Required & Support Vercel/Render One-Click Deployment
- ⭐ **Multiple File Types**: Support Text, Pdf, Docx, Excel, Image, Audio etc.
- 📦 **Multiple Storage Options**: Base64, Local, S3, Cloudflare R2, Min IO, Telegram CDN etc.
- 🔍 **OCR Support**: Extract Text from Image (Require Paddle OCR API)
- 🔊 **Audio Support**: Convert Audio to Text (Require Azure Speech to Text Service)
- 📄 **MarkItDown Support**: Advanced document parsing using Microsoft's MarkItDown library

## Supported File Types
- Text
- Image (_require vision models_)
- Audio (_require Azure Speech to Text Service_)
- Docx (_not support .doc_)
- Pdf
- Pptx (_not support .ppt_)
- Xlsx (_support .xls_)


## Deploy by Docker
> Image: `programzmh/chatnio-blob-service`

```shell
docker run -p 8000:8000 programzmh/chatnio-blob-service

# with environment variables
# docker run -p 8000:8000 -e AZURE_SPEECH_KEY="..." -e AZURE_SPEECH_REGION="..." programzmh/chatnio-blob-service


# if you are using `local` storage type, you need to mount volume (/app/static) to the host
# docker run -p 8000:8000 -v /path/to/static:/app/static programzmh/chatnio-blob-service
```

> Deploy to [Render.com](https://render.com)
> 
> [![Deploy to Render](https://render.com/images/deploy-to-render-button.svg)](https://dashboard.render.com/select-image?type=web&image=programzmh%2Fchatnio-blob-service)
>
> 
> Select **Web Service** and **Docker** Image, then input the image `programzmh/chatnio-blob-service` and click **Create Web Service**.
> > ⭐ Render.com Includes Free **750 Hours** of Usage per Month 
> 

## Deploy by Source Code
The service will be running on `http://localhost:8000`
## Run
```shell
git clone --branch=main https://github.com/Deeptrain-Community/chatnio-blob-service
cd chatnio-blob-service

pip install -r requirements.txt
uvicorn main:app

# enable hot reload
# uvicorn main:app --reload
```


## API
`POST` `/upload` Upload a file
```json
{
    "file": "[file]",
    "enable_ocr": false,
    "enable_vision": true,
    "save_all": false
}
```

| Parameter       | Type    | Description                                                                          |
|-----------------|---------|--------------------------------------------------------------------------------------|
| `file`          | *File   | File to Upload                                                                       |
| `enable_ocr`    | Boolean | Enable OCR (Default: `false`) <br/>**should configure OCR config*                    |
| `enable_vision` | Boolean | Enable Vision (Default: `true`) <br/>**skip if `enable_ocr` is true*                 |
| `save_all`      | Boolean | Save All Images (Default: `false`) <br/>**store all types of files without handling* |


Response

```json
{
  "status": true,
  "type": "pdf",
  "content": "...",
  "error": ""
}
```

| Parameter       | Type     | Description    |
|-----------------|----------|----------------|
| `status`        | Boolean  | Request Status |
| `type`          | String   | File Type      |
| `content`       | String   | File Data      |
| `error`         | String   | Error Message  |

## Environment Variables

### `1` 🎨 General Config (Optional)

- `PDF_MAX_IMAGES`: Max Images Extracted from a PDF File (Default: `10`)
    - **0**: Never Extract Images
    - **-1**: Extract All Images
    - **other**: Extract Top N Images
    - *Tips: The extracted images will be **treated as a normal image** file and directly processed*.
- `MAX_FILE_SIZE`: Max Uploaded File Size MiB (Default: `-1`, No Limit)
  - *Tips: Size limit is also depend on the server configuration (e.g. Nginx/Apache Config, Vercel Free Plan Limit **5MB** Body Size)*
- `CORS_ALLOW_ORIGINS`: CORS Allow Origins (Default: `*`)
  - e.g.: *http://localhost:3000,https://example.com*

### `2` 🔊 Audio Config (Optional)
- `AZURE_SPEECH_KEY`: Azure Speech to Text Service Key (Required for Audio Support)
- `AZURE_SPEECH_REGION`: Azure Speech to Text Service Region (Required for Audio Support)

### `3` 🖼 Storage Config (Optional)
> [!NOTE]
> Storage Config Apply to **Image** Files And `Save All` Option Only.

1. ✨ No Storage (Default)
   - [x] **No Storage Required & No External Dependencies**
   - [x] Base64 Encoding/Decoding
   - [x] Do **Not** Store Anything
   - [x] Support Serverless Deployment **Without Storage** (e.g. Vercel)
   - [ ] No Direct URL Access *(Base64 not support models like `gpt-4-all`)*

2. 📁 Local Storage
   - [ ] **Require Server Environment** (e.g. VPS, Docker)
   - [x] Support Direct URL Access
   - [x] Payless Storage Cost
   - Config:
     - set env `STORAGE_TYPE` to `local` (e.g. `STORAGE_TYPE=local`)
     - set env `LOCAL_STORAGE_DOMAIN` to your deployment domain (e.g. `LOCAL_STORAGE_DOMAIN=http://blob-service.onrender.com`)
     - if you are using Docker, you need to mount volume `/app/static` to the host (e.g. `-v /path/to/static:/app/static`)
     
3. 🚀 [AWS S3](https://aws.amazon.com/s3)
   - [ ] **Payment Storage Cost**
   - [x] Support Direct URL Access
   - [x] China Mainland User Friendly
   - Config:
     - set env `STORAGE_TYPE` to `s3` (e.g. `STORAGE_TYPE=s3`)
     - set env `S3_ACCESS_KEY` to your AWS Access Key ID
     - set env `S3_SECRET_KEY` to your AWS Secret Access Key
     - set env `S3_BUCKET` to your AWS S3 Bucket Name
     - set env `S3_REGION` to your AWS S3 Region

4. 🔔 [Cloudflare R2](https://www.cloudflare.com/zh-cn/developer-platform/r2)
   - [x] **Free Storage Quota ([10GB Storage & Zero Outbound Cost]((https://developers.cloudflare.com/r2/pricing/)))**
   - [x] Support Direct URL Access
   - Config *(S3 Compatible)*:
     - set env `STORAGE_TYPE` to `s3` (e.g. `STORAGE_TYPE=s3`)
     - set env `S3_ACCESS_KEY` to your Cloudflare R2 Access Key ID
     - set env `S3_SECRET_KEY` to your Cloudflare R2 Secret Access Key
     - set env `S3_BUCKET` to your Cloudflare R2 Bucket Name
     - set env `S3_DOMAIN` to your Cloudflare R2 Domain Name (e.g. `https://<account-id>.r2.cloudflarestorage.com`)
     - set env `S3_DIRECT_URL_DOMAIN` to your Cloudflare R2 Public URL Access Domain Name ([Open Public URL Access](https://developers.cloudflare.com/r2/buckets/public-buckets/), e.g. `https://pub-xxx.r2.dev`)

5. 📦 [Min IO](https://min.io)
    - [x] **Self Hosted**
    - [x] Reliable & Flexible Storage
    - Config *(S3 Compatible)*:
      - set env `STORAGE_TYPE` to `s3` (e.g. `STORAGE_TYPE=s3`)
      - set env `S3_SIGN_VERSION` to `s3v4` (e.g. `S3_SIGN_VERSION=s3v4`)
      - set env `S3_ACCESS_KEY` to your Min IO Access Key ID
      - set env `S3_SECRET_KEY` to your Min IO Secret Access Key
      - set env `S3_BUCKET` to your Min IO Bucket Name
      - set env `S3_DOMAIN` to your Min IO Domain Name (e.g. `https://oss.example.com`)
      - *[Optional] If you are using CDN, you can set `S3_DIRECT_URL_DOMAIN` to your Min IO Public URL Access Domain Name (e.g. `https://cdn-hk.example.com`)*

6. ❤ [Telegram CDN](https://github.com/csznet/tgState)
    - [x] **Free Storage (Rate Limit)**
    - [x] Support Direct URL Access *(China Mainland User Unfriendly)*
    - [x] **Limited** File Type & Format
    - [x] Config:
      - set env `STORAGE_TYPE` to `tg` (e.g. `STORAGE_TYPE=tg`)
      - set env `TG_ENDPOINT` to your TG-STATE Endpoint (e.g. `TG_ENDPOINT=https://tgstate.vercel.app`)
      - *[Optional] if you are using password authentication, you can set `TG_PASSWORD` to your TG-STATE Password*

    
### `4` 🔍 OCR Config (Optional)
> [!NOTE]
> OCR Support is based on 👉 [PaddleOCR API](https://github.com/cgcel/PaddleOCRFastAPI) (✔ Self Hosted ✔ Open Source)

- `OCR_ENDPOINT` Paddle OCR Endpoint
    - *e.g.: *http://example.com:8000*

## `5` 📄 MarkItDown Support
You can enable MarkItDown for better document parsing by configuring the following environment variables:

```
MARKITDOWN_ENABLE=true                 # Enable MarkItDown
MARKITDOWN_ENABLE_PLUGINS=false        # Whether to enable plugins
MARKITDOWN_USE_DOCINTEL=false          # Whether to use Document Intelligence
MARKITDOWN_DOCINTEL_ENDPOINT=          # Document Intelligence endpoint
MARKITDOWN_DOCINTEL_KEY=               # Document Intelligence API key
MARKITDOWN_USE_LLM=false               # Whether to use LLM for image descriptions
MARKITDOWN_LLM_MODEL=gpt-4o            # LLM model for image descriptions
MARKITDOWN_LLM_ENDPOINT=               # Custom OpenAI API endpoint (optional)
MARKITDOWN_LLM_API_KEY=                # Custom OpenAI API key (optional)
```

When MarkItDown is enabled, it will be used as the primary processor for supported file types, falling back to the built-in processors if any error occurs.

You can use custom OpenAI API endpoints by setting `MARKITDOWN_LLM_ENDPOINT` to your proxy or mirror site URL (e.g., `https://your-proxy.com/v1`). This is useful if you need to access OpenAI services through a proxy or alternative service provider.

For Azure Document Intelligence, you can provide both the endpoint and API key instead of using DefaultAzureCredential, which provides better security by limiting the permissions of the authentication method.

## Common Errors
- *Cannot Use `Save All` Options Without Storage Config*:
    - This error occurs when you enable `save_all` option without storage config. You need to set `STORAGE_TYPE` to `local` or other storage type to use this option.
- *Trying to upload image with Vision disabled. Enable Vision or OCR to process image*:
    - This error occurs when you disable `enable_vision` and `enable_ocr` at the same time. You need to enable at least one of them to process image files.
- *.ppt files are not supported, only .pptx files are supported*:
    - This error occurs when you upload a old version of Office PowerPoint file. You need to convert it to `.pptx` format to process it.
- *.doc files are not supported, only .docx files are supported*:
    - This error occurs when you upload a old version of Office Word file. You need to convert it to `.docx` format to process it.
- *File Size Limit Exceeded*:
    - This error occurs when you upload a file that exceeds the `MAX_FILE_SIZE` limit. You need to reduce the file size to upload it.
## Development
- **~/config.py**: Env Config
- **~/main.py**: Entry Point
- **~/utils.py**: Utilities
- **~/handlers**: File Handlers
- **~/store**: Storage Handlers
- **~/static**: Static Files (if using **local** storage)

## Tech Stack
- Python & FastAPI

## License
Apache License 2.0
