---
title: "Publish the Pillow Layer"
date: 2026-09-07
weight: 1
chapter: false
pre: " <b> 5.5.1. </b> "
---

## Why a layer
Pillow contains compiled code, so it must be built for Lambda's Linux and Python 3.12, not for your laptop. The command below asks pip for the right Linux wheel.

## Build and publish in CloudShell
1. Open **CloudShell** (the `>_` icon at the top of the console), in `ap-southeast-1`.
2. Run:

```bash
mkdir -p pillow-layer/python
python3 -m pip install --target pillow-layer/python \
  --platform manylinux_2_28_x86_64 --platform manylinux2014_x86_64 \
  --implementation cp --python-version 3.12 --only-binary=:all: Pillow==11.0.0
cd pillow-layer && zip -qr ../pillow-layer.zip python && cd ..
aws lambda publish-layer-version --layer-name pillow-py312 \
  --zip-file fileb://pillow-layer.zip \
  --compatible-runtimes python3.12 --compatible-architectures x86_64 \
  --query LayerVersionArn --output text
```

3. The last command prints the layer ARN, e.g. `arn:aws:lambda:ap-southeast-1:123456789012:layer:pillow-py312:1`.

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Insert screenshot:</b> CloudShell output with the layer ARN → <code>static/images/5-Workshop/5.5/01-layer.png</code></p>
<!-- ![CloudShell output with the layer ARN](/images/5-Workshop/5.5/01-layer.png?width=90pc) -->

{{% notice note %}}
A Python layer must put libraries under a `python/` folder inside the zip; Lambda adds that folder to the import path.
{{% /notice %}}
