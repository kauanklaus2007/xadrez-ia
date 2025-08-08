import pygame
import chess
import chess.engine
import random
import sys

# Inicializa o Pygame
pygame.init()
largura = altura = 512
dimensao = 8
tamanho_casa = largura // dimensao
screen = pygame.display.set_mode((largura, altura))
pygame.display.set_caption("Xadrez com IA")

# Cores
cores = [pygame.Color("white"), pygame.Color("gray")]

# Carrega imagens das peças
pecas_imagens = {}

def carregar_pecas():
    pecas = ['r', 'n', 'b', 'q', 'k', 'p', 'R', 'N', 'B', 'Q', 'K', 'P']
    for p in pecas:
        pecas_imagens[p] = pygame.transform.scale(
            pygame.image.load(f"https://images.chesscomfiles.com/chess-themes/pieces/neo/60/{p}.png"), 
            (tamanho_casa, tamanho_casa)
        )

def desenhar_tabuleiro(board):
    for r in range(dimensao):
        for c in range(dimensao):
            cor = cores[(r + c) % 2]
            pygame.draw.rect(screen, cor, pygame.Rect(c * tamanho_casa, r * tamanho_casa, tamanho_casa, tamanho_casa))

def desenhar_pecas(tabuleiro):
    for r in range(dimensao):
        for c in range(dimensao):
            peca = tabuleiro.piece_at(r * 8 + c)
            if peca:
                screen.blit(pecas_imagens[peca.symbol()],
                            pygame.Rect(c * tamanho_casa, r * tamanho_casa, tamanho_casa, tamanho_casa))

# Função para IA mover aleatoriamente
def ia_mover(tabuleiro):
    movimentos = list(tabuleiro.legal_moves)
    if movimentos:
        movimento = random.choice(movimentos)
        tabuleiro.push(movimento)

def main():
    clock = pygame.time.Clock()
    tabuleiro = chess.Board()
    carregar_pecas()
    selecionado = None
    rodando = True

    while rodando:
        for evento in pygame.event.get():
            if evento.type == pygame.QUIT:
                rodando = False
                pygame.quit()
                sys.exit()

            if evento.type == pygame.MOUSEBUTTONDOWN:
                pos = pygame.mouse.get_pos()
                coluna = pos[0] // tamanho_casa
                linha = pos[1] // tamanho_casa
                quadrado = chess.square(coluna, 7 - linha)

                if selecionado is None:
                    if tabuleiro.piece_at(quadrado) and tabuleiro.piece_at(quadrado).color == chess.WHITE:
                        selecionado = quadrado
                else:
                    movimento = chess.Move(selecionado, quadrado)
                    if movimento in tabuleiro.legal_moves:
                        tabuleiro.push(movimento)
                        ia_mover(tabuleiro)
                    selecionado = None

        desenhar_tabuleiro(tabuleiro)
        desenhar_pecas(tabuleiro)
        pygame.display.flip()
        clock.tick(15)

if __name__ == "__main__":
    main()
