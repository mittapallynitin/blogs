# Deploying ML Models using Flask + Docker

*Published on March 10, 2024*

## Introduction

Deploying machine learning models in production can be challenging. In this guide, we'll walk through the process of containerizing a Flask application that serves an ML model using Docker. This approach ensures consistency across different environments and makes deployment much simpler.

## Prerequisites

- Python 3.8+
- Docker installed on your system
- Basic understanding of Flask and Docker
- A trained ML model (we'll use a simple example)

## Project Structure

```
ml-app/
├── Dockerfile
├── requirements.txt
├── app.py
├── model/
│   └── model.pkl
└── README.md
```

## Step 1: Create the Flask Application

```python
from flask import Flask, request, jsonify
import pickle
import numpy as np

app = Flask(__name__)

# Load the model
with open('model/model.pkl', 'rb') as f:
    model = pickle.load(f)

@app.route('/predict', methods=['POST'])
def predict():
    data = request.get_json()
    features = np.array(data['features']).reshape(1, -1)
    prediction = model.predict(features)
    return jsonify({'prediction': prediction.tolist()})

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

## Step 2: Create the Dockerfile

```dockerfile
FROM python:3.8-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 5000

CMD ["python", "app.py"]
```

## Step 3: Create requirements.txt

```
flask==2.0.1
numpy==1.21.0
scikit-learn==0.24.2
```

## Step 4: Build and Run the Docker Container

```bash
# Build the image
docker build -t ml-app .

# Run the container
docker run -p 5000:5000 ml-app
```

## Testing the Deployment

You can test the API using curl:

```bash
curl -X POST http://localhost:5000/predict \
     -H "Content-Type: application/json" \
     -d '{"features": [1, 2, 3, 4]}'
```

## Best Practices

1. **Environment Variables**: Use environment variables for configuration
2. **Health Checks**: Implement health check endpoints
3. **Logging**: Set up proper logging
4. **Security**: Implement authentication and input validation
5. **Monitoring**: Add monitoring and metrics collection

## Scaling Considerations

- Use Docker Compose for multi-container applications
- Consider using Kubernetes for orchestration
- Implement load balancing
- Set up CI/CD pipelines

## Conclusion

Dockerizing your ML application with Flask provides a robust and scalable solution for deployment. This setup ensures consistency across environments and makes it easier to manage dependencies and configurations.

## Further Reading

- [Docker Documentation](https://docs.docker.com/)
- [Flask Documentation](https://flask.palletsprojects.com/)
- [MLflow for Model Management](https://www.mlflow.org/) 