# Monorepo Guide: Deployment from A to Z

## Introduction
This repository serves as a guide for structuring, integrating, and deploying scripts using a standardized approach. It includes an example "Hello, World!" script, a wrapper integration, and Dockerization.

## Repository Structure
```
Scipts/
│── hello_world/
│   ├── hello.py        # Example Hello World script
│   ├── gateway.py      # Wrapper integration
│   ├── Dockerfile      # Dockerization
│   ├── .env           # Environment variables
│   ├── requirements.txt # Dependencies
│── README.md           # Deployment documentation
```

---

## 1. Creating the "Hello, World!" Script

```python
# hello.py
import os
from python_wrapper import command, expose

def hello():
    """Simple Hello World function."""
    return {"message": "Hello, World!"}

@command("say_hello")
def say_hello(args, **kwargs):
    return hello()

expose(host='0.0.0.0', port=5000)
```

### 2. Creating the Gateway Wrapper
```python
# gateway.py
import os
from hello import say_hello
from python_wrapper import command, expose

@command("say_hello")
def say_hello_command(args, **kwargs):
    return say_hello(args)

expose(host='0.0.0.0', port=5000)
```
### 3. Creating requirements.txt
```shell
# requirements.txt
.
.
.
// DO NOT USE IN PROD
python_wrapper @ git+https://<YOUR_GITHUB_USERNAME>:<YOUR_GITHUB_PAT>@github.com/VALINNOV/auditguard-wrappers.git@91a42a1c31872299b31fdc85d36faedc62b08d2d#subdirectory=python
.
.
.
```
### 3. Creating the Dockerfile
```dockerfile
# Dockerfile
FROM python:3.9

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

CMD ["python", "gateway.py"]
```

### 4. Creating the .env File
```ini
# .env
API_ENDPOINT=http://localhost:5000
API_KEY=your_api_key_here
```

### 5. Running the Project
#### Step 1: Build the Docker Image
```sh
docker build -t hello-world-app .
```

#### Step 2: Run the Container
```sh
docker run -d --name hello_container --env-file .env -p 5000:5000 hello-world-app
```

#### Step 3: Test the API
```sh
curl http://localhost:5000/api/say_hello
```

This will return:
```json
{"message": "Hello, World!"}
```

---

## Conclusion
This guide provides a step-by-step approach to creating, wrapping, and deploying a script in a structured monorepo. The same methodology can be applied to more complex scripts like the Wappalyzer scanner.
