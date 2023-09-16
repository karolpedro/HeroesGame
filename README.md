import tkinter as tk
from tkinter import messagebox
import random

# Definir respostaCorreta como uma variável global
respostaCorreta = None

def gerar_pergunta():
    global respostaCorreta  # Tornar respostaCorreta global
    num1 = random.randint(1, 10)
    num2 = random.randint(1, 10)
    operacao = random.choice(["+", "-"])
    
    resultado = 0
    
    if operacao == "+":
        resultado = num1 + num2
    else:
        resultado = num1 - num2
    
    pergunta = f"{num1} {operacao} {num2} = ?"
    respostaCorreta = resultado  # Definir respostaCorreta
    return pergunta

def verificar_resposta():
    resposta = entrada_resposta.get()
    try:
        resposta = int(resposta)
    except ValueError:
        messagebox.showerror("Erro", "Digite um número válido")
        return
    
    global pontuacao, num_perguntas
    if resposta == respostaCorreta:
        messagebox.showinfo("Resposta Correta", "Resposta correta!")
        pontuacao += 1
        if pontuacao < num_perguntas:
            proxima_pergunta()
        else:
            messagebox.showinfo("Fim do Jogo", f"Sua pontuação final é: {pontuacao}/{num_perguntas}")
            janela.quit()
    else:
        messagebox.showerror("Resposta Incorreta", "Resposta incorreta.")
        entrada_resposta.delete(0, tk.END)

def proxima_pergunta():
    pergunta = gerar_pergunta()
    label_pergunta.config(text=pergunta)
    entrada_resposta.delete(0, tk.END)

pontuacao = 0
num_perguntas = 5

janela = tk.Tk()
janela.title("Jogo de Adição e Subtração")

label_pergunta = tk.Label(janela, text="", font=("Arial", 14))
label_pergunta.pack(pady=20)

entrada_resposta = tk.Entry(janela, font=("Arial", 14))
entrada_resposta.pack(pady=10)

botao_verificar = tk.Button(janela, text="Verificar Resposta", command=verificar_resposta)
botao_verificar.pack()

proxima_pergunta()

janela.mainloop()
