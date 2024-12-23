import tkinter as tk
from tkinter import messagebox
import sqlite3

# Conexão com o banco de dados SQLite
def conectar_db():
    conn = sqlite3.connect('delivery.db')
    cursor = conn.cursor()
    cursor.execute('''
        CREATE TABLE IF NOT EXISTS pedidos (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            nome TEXT NOT NULL,
            endereco TEXT NOT NULL,
            comida TEXT NOT NULL
        )
    ''')
    conn.commit()
    return conn

# Função para adicionar um pedido
def adicionar_pedido():
    nome = entry_nome.get()
    endereco = entry_endereco.get()
    comida = entry_comida.get()
    
    if nome and endereco and comida:
        conn = conectar_db()
        cursor = conn.cursor()
        cursor.execute('INSERT INTO pedidos (nome, endereco, comida) VALUES (?, ?, ?)', (nome, endereco, comida))
        conn.commit()
        conn.close()
        messagebox.showinfo("Sucesso", "Pedido adicionado com sucesso!")
        entry_nome.delete(0, tk.END)
        entry_endereco.delete(0, tk.END)
        entry_comida.delete(0, tk.END)
    else:
        messagebox.showwarning("Atenção", "Por favor, preencha todos os campos.")

# Interface Gráfica
app = tk.Tk()
app.title("Aplicativo de Delivery")

tk.Label(app, text="Nome:").pack()
entry_nome = tk.Entry(app)
entry_nome.pack()

tk.Label(app, text="Endereço:").pack()
entry_endereco = tk.Entry(app)
entry_endereco.pack()

tk.Label(app, text="Comida:").pack()
entry_comida = tk.Entry(app)
entry_comida.pack()

tk.Button(app, text="Adicionar Pedido", command=adicionar_pedido).pack()

app.mainloop()
Explicação do Código
Banco de Dados: O código cria um banco de dados SQLite chamado delivery.db e uma tabela pedidos para armazenar os pedidos.
Interface Gráfica: Usamos o Tkinter para criar uma interface simples onde o usuário pode inserir o nome, endereço e tipo de comida.
Adicionar Pedido: A função adicionar_pedido insere os dados no banco de dados e exibe uma mensagem de sucesso.
Próximos Passos
Melhorias: Você pode expandir o aplicativo adicionando funcionalidades como:
Listar pedidos existentes.
Editar ou excluir pedidos.
Implementar um sistema de login para usuários.
Se precisar de mais detalhes ou ajuda com alguma parte específica, é só avisar! Estou aqui para ajudar! 😊

Fontes
Sistema De (Delivery)Entrega De Pizza Python Tkinter Sqlite3 Com ...

youtube.com
Pythonando/Sistema-delivery-Imersao-Python - GitHub

github.com
DeliverApp 1 - FLET + PYTHON + SQLITE3 - YouTube

youtube.com
Python: crie a sua primeira aplicação | Alura Cursos Online
alura.com.br
Sistema de Gerenciamento de Pedidos - GitHub

github.com
faça essa melhoria agora
Ok! Vamos adicionar a funcionalidade de listar, editar e excluir pedidos ao nosso aplicativo de delivery.

import tkinter as tk
from tkinter import messagebox
import sqlite3

# Conexão com o banco de dados SQLite
def conectar_db():
    conn = sqlite3.connect('delivery.db')
    cursor = conn.cursor()
    cursor.execute('''
        CREATE TABLE IF NOT EXISTS pedidos (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            nome TEXT NOT NULL,
            endereco TEXT NOT NULL,
            comida TEXT NOT NULL
        )
    ''')
    conn.commit()
    return conn

# Função para adicionar um pedido
def adicionar_pedido():
    nome = entry_nome.get()
    endereco = entry_endereco.get()
    comida = entry_comida.get()
    
    if nome and endereco and comida:
        conn = conectar_db()
        cursor = conn.cursor()
        cursor.execute('INSERT INTO pedidos (nome, endereco, comida) VALUES (?, ?, ?)', (nome, endereco, comida))
        conn.commit()
        conn.close()
        messagebox.showinfo("Sucesso", "Pedido adicionado com sucesso!")
        entry_nome.delete(0, tk.END)
        entry_endereco.delete(0, tk.END)
        entry_comida.delete(0, tk.END)
        atualizar_lista_pedidos()
    else:
        messagebox.showwarning("Atenção", "Por favor, preencha todos os campos.")

# Função para listar pedidos
def atualizar_lista_pedidos():
    listbox_pedidos.delete(0, tk.END)
    conn = conectar_db()
    cursor = conn.cursor()
    cursor.execute('SELECT * FROM pedidos')
    pedidos = cursor.fetchall()
    conn.close()
    for pedido in pedidos:
        listbox_pedidos.insert(tk.END, f"ID: {pedido[0]} - Nome: {pedido[1]} - Endereço: {pedido[2]} - Comida: {pedido[3]}")

# Função para editar um pedido
def editar_pedido():
    try:
        id_pedido = int(listbox_pedidos.get(tk.ANCHOR))
        conn = conectar_db()
        cursor = conn.cursor()
        cursor.execute('SELECT * FROM pedidos WHERE id=?', (id_pedido,))
        pedido = cursor.fetchone()
        if pedido:
            entry_nome.delete(0, tk.END)
            entry_endereco.delete(0, tk.END)
            entry_comida.delete(0, tk.END)
            entry_nome.insert(0, pedido[1])
            entry_endereco.insert(0, pedido[2])
            entry_comida.insert(0, pedido[3])
            
            def confirmar_edicao():
                novo_nome = entry_nome.get()
                novo_endereco = entry_endereco.get()
                nova_comida = entry_comida.get()
                if novo_nome and novo_endereco and nova_comida:
                    cursor.execute('UPDATE pedidos SET nome=?, endereco=?, comida=? WHERE id=?', (novo_nome, novo_endereco, nova_comida, id_pedido))
                    conn.commit()
                    conn.close()
                    messagebox.showinfo("Sucesso", "Pedido atualizado com sucesso!")
                    atualizar_lista_pedidos()
                    entry_nome.delete(0, tk.END)
                    entry_endereco.delete(0, tk.END)
                    entry_comida.delete(0, tk.END)
                else:
                    messagebox.showwarning("Atenção", "Por favor, preencha todos os campos.")
            
            tk.Button(app, text="Confirmar Edição", command=confirmar_edicao).pack()
        else:
            messagebox.showwarning("Atenção", "Pedido não encontrado.")
        conn.close()
    except ValueError:
        messagebox.showwarning("Atenção", "Selecione um pedido válido para editar.")

# Função para excluir um pedido
def excluir_pedido():
    try:
        id_pedido = int(listbox_pedidos.get(tk.ANCHOR))
        conn = conectar_db()
        cursor = conn.cursor()
        cursor.execute('DELETE FROM pedidos WHERE id=?', (id_pedido,))
        conn.commit()
        conn.close()
        messagebox.showinfo("Sucesso", "Pedido excluído com sucesso!")
        atualizar_lista_pedidos()
    except ValueError:
        messagebox.showwarning("Atenção", "Selecione um pedido válido para excluir.")

# Interface Gráfica
app = tk.Tk()
app.title("Aplicativo de Delivery")

tk.Label(app, text="Nome:").pack()
entry_nome = tk.Entry(app)
entry_nome.pack()

tk.Label(app, text="Endereço:").pack()
entry_endereco = tk.Entry(app)
entry_endereco.pack()

tk.Label(app, text="Comida:").pack()
entry_comida = tk.Entry(app)
entry_comida.pack()

tk.Button(app, text="Adicionar Pedido", command=adicionar_pedido).pack()

tk.Label(app, text="Pedidos:").pack()
listbox_pedidos = tk.Listbox(app)
listbox_pedidos.pack()

tk.Button(app, text="Atualizar Lista", command=atualizar_lista_pedidos).pack()
tk.Button(app, text="Editar Pedido", command=editar_pedido).pack()
tk.Button(app, text="Excluir Pedido", command=excluir_pedido).pack()

atualizar_lista_pedidos()

app.mainloop()
