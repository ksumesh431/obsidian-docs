`docker run -d -p 3000:8080 --gpus all --network=host --add-host=host.docker.internal:host-gateway -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:cuda`

#openwebui 