Configuración de Google Cloud Docker, Nginx y Cerbot para n8n

Paso 1: Actualizar el sistema


sudo apt update


Paso 2: Instalar Docker


sudo apt install docker.io


Paso 3: Iniciar el servicio Docker


sudo systemctl start docker


Paso 4: Habilitar el servicio Docker


sudo systemctl enable docker


Paso 5: Ejecutar el contenedor Docker N8N


sudo docker run -d --restart unless-stopped -it \
--name n8n \
-p 5678:5678 \
-e N8N_HOST="your-domain.com" \
-e WEBHOOK_TUNNEL_URL="https://your-domain.com/" \
-e WEBHOOK_URL="https://your-domain.com/" \
-v ~/.n8n:/root/.n8n \
n8nio/n8n


Paso 6: Instalar Nginx


sudo apt install nginx


Paso 7: Cree un archivo de configuración de Nginx para N8N


sudo nano /etc/nginx/sites-available/n8n


Añade el siguiente contenido:

server {
    listen 80;
    server_name your-domain.com;

    location / {
        proxy_pass http://localhost:5678;
        proxy_http_version 1.1;
        chunked_transfer_encoding off;
        proxy_buffering off;
        proxy_cache off;

        # Headers for WebSocket support
        proxy_set_header Connection 'Upgrade';
        proxy_set_header Upgrade $http_upgrade;

        # Additional headers for forwarding client info
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}

Guardar y salir usando:

CTRL+O, ENTER, CTRL+X


Paso 8: Habilitar la configuración de Nginx


sudo ln -s /etc/nginx/sites-available/n8n /etc/nginx/sites-enabled/


Paso 9: Probar la configuración de Nginx


sudo nginx -t


Paso 10: Reiniciar el servicio Nginx


sudo systemctl restart nginx


Paso 11: Instalar Certbot para certificados SSL


sudo apt install certbot python3-certbot-nginx


Paso 12: Obtener el certificado SSL


sudo certbot --nginx -d your-domain.com


Paso 14: Reiniciar el servicio Nginx


sudo systemctl restart nginx

