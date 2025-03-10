from tkinter import *
from tkinter.messagebox import showinfo
import random

largura = 600
altura = 600
tamanho_grade = 20
velocidade = 10  # Velocidade

# Classe que representa os quadrados   cobra ou comida
class quadrado:
    def __init__(self, x, y, cor):
        self.x = x
        self.y = y
        self.cor = cor
        self.dim = [0, 0, 0, tamanho_grade, tamanho_grade, tamanho_grade, tamanho_grade, 0]

    def pos(self):
        return [self.dim[0] + self.x, self.dim[1] + self.y, self.dim[2] + self.x, self.dim[3] + self.y, 
                self.dim[4] + self.x, self.dim[5] + self.y, self.dim[6] + self.x, self.dim[7] + self.y]


class Jogo:
    def __init__(self):
        self.janela = Tk()
        self.canvas = Canvas(self.janela, bg='green', width=largura, height=altura)  # Fundo verde
        self.canvas.pack()

        # Inicializa a cobra com 4 blocos
        self.cobra = [
            quadrado(100, 100, 'darkgreen'),
            quadrado(80, 100, 'darkgreen'),
            quadrado(60, 100, 'darkgreen'),
            quadrado(40, 100, 'darkgreen')
        ]
        self.alimento = quadrado(
            random.randint(0, (largura // tamanho_grade) - 1) * tamanho_grade,
            random.randint(0, (altura // tamanho_grade) - 1) * tamanho_grade,
            'red'
        )

        self.velocidade = [tamanho_grade, 0]  # Velocidade inicial

        # Controle
        self.janela.bind("<Up>", self.moverCima)
        self.janela.bind("<Down>", self.moverBaixo)
        self.janela.bind("<Right>", self.moverDireita)
        self.janela.bind("<Left>", self.moverEsquerda)

    def moverCima(self, evento):
        if self.velocidade != [0, tamanho_grade]:
            self.velocidade = [0, -tamanho_grade]

    def moverBaixo(self, evento):
        if self.velocidade != [0, -tamanho_grade]:
            self.velocidade = [0, tamanho_grade]

    def moverDireita(self, evento):
        if self.velocidade != [-tamanho_grade, 0]:
            self.velocidade = [tamanho_grade, 0]

    def moverEsquerda(self, evento):
        if self.velocidade != [tamanho_grade, 0]:
            self.velocidade = [-tamanho_grade, 0]

    def rodar(self):
        def loop_do_jogo():
            self.canvas.delete('all')

            # Atualiza a posição do corpo da cobra
            for i in range(len(self.cobra) - 1, 0, -1):
                self.cobra[i].x = self.cobra[i - 1].x
                self.cobra[i].y = self.cobra[i - 1].y

            # Atualiza a posição da cabeça
            self.cobra[0].x += self.velocidade[0]
            self.cobra[0].y += self.velocidade[1]

            # Verificação de colisão entre a cabeça da cobra e o alimento
            if self.cobra[0].x == self.alimento.x and self.cobra[0].y == self.alimento.y:
                self.alimento.x = random.randint(0, (largura // tamanho_grade) - 1) * tamanho_grade
                self.alimento.y = random.randint(0, (altura // tamanho_grade) - 1) * tamanho_grade
                novo_segmento = quadrado(self.cobra[-1].x, self.cobra[-1].y, 'darkgreen')
                self.cobra.append(novo_segmento)

            # Desenha a cobra
            for q in self.cobra:
                self.canvas.create_polygon(q.pos(), fill=q.cor)

            # Desenha a comida
            self.canvas.create_polygon(self.alimento.pos(), fill=self.alimento.cor)

            # Verifica colisões com a borda
            if self.cobra[0].x < 0 or self.cobra[0].x >= largura or self.cobra[0].y < 0 or self.cobra[0].y >= altura:
                self.fim_de_jogo()

            # Verifica colisão com ela mesma
            for i in range(1, len(self.cobra)):
                if self.cobra[0].x == self.cobra[i].x and self.cobra[0].y == self.cobra[i].y:
                    self.fim_de_jogo()

             # Chama o loop novamente dps de 100ms
            self.canvas.after(100, loop_do_jogo)

        loop_do_jogo()
        self.janela.mainloop()

    def fim_de_jogo(self):
        showinfo(title="Fim de Jogo", message="Você perdeu! Tente novamente.")
        self.janela.quit()

# Iniciar
jogo = Jogo()
jogo.rodar()
