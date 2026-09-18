import pygame
import random

# Inicializar Pygame
pygame.init()

# -----------------------------
# CONFIGURACIÓN DE LA VENTANA
# -----------------------------
ANCHO = 800
ALTO = 600

pantalla = pygame.display.set_mode((ANCHO, ALTO))
pygame.display.set_caption("Esquiva los obstáculos")

# Control de FPS
reloj = pygame.time.Clock()

# -----------------------------
# COLORES
# -----------------------------
NEGRO = (0, 0, 0)
BLANCO = (255, 255, 255)
AZUL = (50, 100, 255)
ROJO = (255, 50, 50)
VERDE = (50, 200, 50)

# -----------------------------
# JUGADOR
# -----------------------------
jugador = pygame.Rect(375, 520, 50, 50)
velocidad_jugador = 7

# -----------------------------
# OBSTÁCULOS
# -----------------------------
obstaculos = []
velocidad_obstaculos = 5

# -----------------------------
# PUNTAJE Y VIDAS
# -----------------------------
puntaje = 0
vidas = 3

# Fuentes
fuente = pygame.font.Font(None, 36)
fuente_grande = pygame.font.Font(None, 70)

# Variable para controlar el juego
jugando = True
game_over = False


# -----------------------------
# CREAR OBSTÁCULO
# -----------------------------
def crear_obstaculo():
    x = random.randint(0, ANCHO - 40)
    y = -40

    obstaculo = pygame.Rect(x, y, 40, 40)
    obstaculos.append(obstaculo)


# -----------------------------
# REINICIAR JUEGO
# -----------------------------
def reiniciar_juego():
    global jugador, obstaculos, puntaje, vidas, game_over

    jugador.x = 375
    jugador.y = 520

    obstaculos = []
    puntaje = 0
    vidas = 3
    game_over = False


# -----------------------------
# BUCLE PRINCIPAL
# -----------------------------
contador = 0

while jugando:

    # Controlar eventos
    for evento in pygame.event.get():

        if evento.type == pygame.QUIT:
            jugando = False

        # Si estamos en Game Over
        if game_over:
            if evento.type == pygame.KEYDOWN:

                # R = reiniciar
                if evento.key == pygame.K_r:
                    reiniciar_juego()

                # ESC = salir
                if evento.key == pygame.K_ESCAPE:
                    jugando = False

    # -----------------------------
    # ACTUALIZAR JUEGO
    # -----------------------------
    if not game_over:

        # Movimiento del jugador
        teclas = pygame.key.get_pressed()

        if teclas[pygame.K_LEFT]:
            jugador.x -= velocidad_jugador

        if teclas[pygame.K_RIGHT]:
            jugador.x += velocidad_jugador

        if teclas[pygame.K_UP]:
            jugador.y -= velocidad_jugador

        if teclas[pygame.K_DOWN]:
            jugador.y += velocidad_jugador

        # Evitar que salga de la pantalla
        if jugador.left < 0:
            jugador.left = 0

        if jugador.right > ANCHO:
            jugador.right = ANCHO

        if jugador.top < 0:
            jugador.top = 0

        if jugador.bottom > ALTO:
            jugador.bottom = ALTO

        # Crear obstáculos cada cierto tiempo
        contador += 1

        if contador >= 30:
            crear_obstaculo()
            contador = 0

        # Mover obstáculos
        for obstaculo in obstaculos[:]:
            obstaculo.y += velocidad_obstaculos

            # Si sale de la pantalla
            if obstaculo.top > ALTO:
                obstaculos.remove(obstaculo)
                puntaje += 1

            # Detectar choque
            elif jugador.colliderect(obstaculo):
                obstaculos.remove(obstaculo)
                vidas -= 1

                if vidas <= 0:
                    game_over = True

    # -----------------------------
    # DIBUJAR PANTALLA
    # -----------------------------
    pantalla.fill(NEGRO)

    # Dibujar jugador
    pygame.draw.rect(pantalla, AZUL, jugador)

    # Dibujar obstáculos
    for obstaculo in obstaculos:
        pygame.draw.rect(pantalla, ROJO, obstaculo)

    # Mostrar puntaje
    texto_puntaje = fuente.render(
        "Puntaje: " + str(puntaje),
        True,
        BLANCO
    )

    pantalla.blit(texto_puntaje, (20, 20))

    # Mostrar vidas
    texto_vidas = fuente.render(
        "Vidas: " + str(vidas),
        True,
        BLANCO
    )

    pantalla.blit(texto_vidas, (650, 20))

    # -----------------------------
    # GAME OVER
    # -----------------------------
    if game_over:

        texto_game_over = fuente_grande.render(
            "GAME OVER",
            True,
            ROJO
        )

        pantalla.blit(
            texto_game_over,
            (ANCHO // 2 - 170, 220)
        )

        texto_reiniciar = fuente.render(
            "Presiona R para reiniciar",
            True,
            BLANCO
        )

        pantalla.blit(
            texto_reiniciar,
            (ANCHO // 2 - 150, 310)
        )

        texto_salir = fuente.render(
            "Presiona ESC para salir",
            True,
            BLANCO
        )

        pantalla.blit(
            texto_salir,
            (ANCHO // 2 - 140, 350)
        )

    # Actualizar pantalla
    pygame.display.flip()

    # Mantener 60 FPS
    reloj.tick(60)


# Cerrar Pygame
pygame.quit()
