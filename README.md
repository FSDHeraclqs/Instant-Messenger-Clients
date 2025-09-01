import socket
import threading

HOST = '127.0.0.1'  # 修改为服务器的 IP 地址
PORT = 12345

def receive_messages(client_socket):
    while True:
        try:
            message = client_socket.recv(1024).decode()
            if message:
                print("\n" + message)
        except:
            print("[!] Connection closed.")
            break

def send_messages(client_socket):
    while True:
        message = input()
        if message.lower() == 'exit':
            client_socket.close()
            break
        client_socket.send(message.encode())

def start_client():
    client = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    try:
        client.connect((HOST, PORT))
    except:
        print("[!] Could not connect to server.")
        return

    print(f"[+] Connected to server at {HOST}:{PORT}")
    username = input("Enter your name: ")
    client.send(f"{username} has joined the chat.".encode())

    # Start receiving and sending threads
    threading.Thread(target=receive_messages, args=(client,), daemon=True).start()
    send_messages(client)

if __name__ == "__main__":
    start_client()
