import random
import tkinter as tk
from tkinter import messagebox

class CacaPalavras:
    def __init__(self, janela):
        self.janela = janela
        self.janela.title("Caça-Palavras")

        # Tamanho da grade (aqui, 15x15)
        self.linhas = 15
        self.colunas = 15

        # Lista de palavras para o caça-palavras
        self.palavras = ["PASSARO", "ELEFANTE", "CASA", "ANIMAL", "MOSAICO", "GIRAFA", "LOBO"]

        # Criação da grade
        self.grade = []
        self.selecao = []  # Lista de células selecionadas
        for i in range(self.linhas):
            linha = []
            for j in range(self.colunas):
                letra = random.choice('ABCDEFGHIJKLMNOPQRSTUVWXYZ')
                label = tk.Label(self.janela, text=letra, width=2, height=1)
                label.grid(row=i, column=j)
                label.bind("<Button-1>", lambda event, i=i, j=j: self.selecionar_celula(i, j))
                linha.append(label)
            self.grade.append(linha)

        # Coloca as palavras na grade (horizontalmente ou verticalmente)
        for palavra in self.palavras:
            orientacao = random.choice(["horizontal", "vertical"])
            posicoes_validas = []
            if orientacao == "horizontal":
                for i in range(self.linhas):
                    for j in range(self.colunas - len(palavra) + 1):
                        posicoes_validas.append((i, j))
            else:  # orientacao == "vertical"
                for i in range(self.linhas - len(palavra) + 1):
                    for j in range(self.colunas):
                        posicoes_validas.append((i, j))

            if posicoes_validas:
                i, j = random.choice(posicoes_validas)
                for letra in palavra:
                    self.grade[i][j]["text"] = letra
                    j += 1 if orientacao == "horizontal" else 0
                    i += 1 if orientacao == "vertical" else 0

        # Botões para verificar palavras
        self.verificar_horizontal = tk.Button(self.janela, text="Verificar Horizontal", command=self.verificar_palavras_horizontal)
        self.verificar_horizontal.grid(row=self.linhas + 1, column=0, columnspan=self.colunas)
        
        self.verificar_vertical = tk.Button(self.janela, text="Verificar Vertical", command=self.verificar_palavras_vertical)
        self.verificar_vertical.grid(row=self.linhas + 2, column=0, columnspan=self.colunas)

        # Botão para limpar seleção
        self.limpar_selecao = tk.Button(self.janela, text="Limpar Seleção", command=self.limpar_selecao_palavra)
        self.limpar_selecao.grid(row=self.linhas + 3, column=0, columnspan=self.colunas)

    def selecionar_celula(self, i, j):
        # Adiciona a célula à lista de seleção
        self.selecao.append((i, j))
        self.grade[i][j].config(bg="yellow")

    def limpar_selecao_palavra(self):
        # Limpa a seleção de células
        for i, j in self.selecao:
            self.grade[i][j].config(bg=None)  # Remove o destaque
        self.selecao = []  # Limpa a lista de seleção

    def verificar_palavras_horizontal(self):
        for palavra in self.palavras:
            for i in range(self.linhas):
                linha = "".join([self.grade[i][j]["text"] for j in range(self.colunas)])
                if palavra in linha:
                    for j in range(self.colunas - len(palavra) + 1):
                        if linha[j:j+len(palavra)] == palavra:
                            for k in range(j, j+len(palavra)):
                                self.selecionar_celula(i, k)

    def verificar_palavras_vertical(self):
        for palavra in self.palavras:
            for j in range(self.colunas):
                coluna = "".join([self.grade[i][j]["text"] for i in range(self.linhas)])
                if palavra in coluna:
                    for i in range(self.linhas - len(palavra) + 1):
                        if coluna[i:i+len(palavra)] == palavra:
                            for k in range(i, i+len(palavra)):
                                self.selecionar_celula(k, j)


pares_animais = [
    (2, 'CACHORRO'),
    (5, 'GATO'),
    (7, 'ELEFANTE'),
    (3, 'LEÃO'),
    (8, 'GIRAFA'),
    (6, 'TIGRE'),
    (9, 'URSO'),
    # Adicione mais pares de números e nomes de animais conforme desejar
]

# Variáveis globais para armazenar a pergunta atual
num1, operador, num2, resultado, animal = 0, '+', 0, 0, ''

def gerar_pergunta():
    global num1, operador, num2, resultado, animal
    num1, animal = random.choice(pares_animais)
    num2 = random.randint(1, 10)
    operador = random.choice(['+', '-'])
    if operador == '-':
        # Garante que não haverá resultado negativo, invertendo num1 e num2 se necessário
        num1, num2 = max(num1, num2), min(num1, num2)
    resultado = num1 + num2 if operador == '+' else num1 - num2
    return num1, operador, num2, resultado, animal

def obter_resposta_correta(resposta):
    if operador == '+':
        return num1 + num2 == resultado and resposta == resultado
    else:
        return num1 - num2 == resultado and resposta == resultado

def verificar_resposta():
    global resposta_entry
    resposta_usuario = resposta_entry.get()
    if resposta_usuario.isdigit():
        resposta_usuario = int(resposta_usuario)
        if obter_resposta_correta(resposta_usuario):
            messagebox.showinfo("Resultado", f"PARABÉNS! O animal é um {animal}.")
            proxima_pergunta()
        else:
            messagebox.showerror("Resultado", f"TENTE OUTRA VEZ. A resposta correta era {resultado}.")
    else:
        messagebox.showerror("Erro", "Digite um número válido.")

def proxima_pergunta():
    global num1, operador, num2, resultado, animal
    num1, operador, num2, resultado, animal = gerar_pergunta()
    pergunta_label.config(text=f"Quanto é {num1} {operador} {num2}?")
    resposta_entry.delete(0, tk.END)

def open_heroes_window():
    heroes_window = tk.Toplevel(root)
    heroes_window.title("Heroes")
    heroes_window.geometry("800x800")
    
    gerar_pergunta()

    global pergunta_label, resposta_entry
    pergunta_label = tk.Label(heroes_window, text=f"QUANTO É {num1} {operador} {num2}?", font=("Arial", 18))
    pergunta_label.pack(pady=20)

    resposta_entry = tk.Entry(heroes_window, font=("Arial", 16))
    resposta_entry.pack()

    verificar_button = tk.Button(heroes_window, text="RESPOSTA", command=verificar_resposta, font=("Arial", 16))
    verificar_button.pack(pady=10)

    proxima_pergunta_button = tk.Button(heroes_window, text="PRÓXIMA PERGUNTA", command=proxima_pergunta, font=("Arial", 16))
    proxima_pergunta_button.pack(pady=10)

def open_cacapalavras_window():
    cacapalavras_window = tk.Toplevel(root)
    cacapalavras_window.title("Caça Palavras")
    cacapalavras_window.geometry("800x800")
    
    jogo = CacaPalavras(cacapalavras_window)

# Cria a janela principal
root = tk.Tk()
root.title("Minha Interface")
root.geometry("800x800")

# Aumenta o tamanho dos botões e centraliza-os na janela principal
heroes_button = tk.Button(root, text="Heroes", command=open_heroes_window, width=20, height=5)
cacapalavras_button = tk.Button(root, text="Caça Palavras", command=open_cacapalavras_window, width=20, height=5)

# Centraliza os botões na janela principal
heroes_button.pack(side=tk.TOP, pady=20)
cacapalavras_button.pack(side=tk.TOP)

# Inicia o loop principal da interface
root.mainloop()
