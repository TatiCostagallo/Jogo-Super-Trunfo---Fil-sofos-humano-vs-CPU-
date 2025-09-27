# Jogo-Super-Trunfo---Fil-sofos-humano-vs-CPU-
#!/usr/bin/env python3
# super_trunfo_filosofos.py
# Jogo Super Trunfo - Filósofos (humano vs CPU)
# Rodar: python super_trunfo_filosofos.py

import random
import sys
import textwrap

# --- Definição do baralho (12 cartas) ---
# Cada carta: nome, descricao curta, atributos (influencia, originalidade, complexidade, longevidade)
# Platão é o Super Trunfo (super_trunfo=True)
def create_deck():
    return [
        {"nome":"Platão", "descricao":"Filósofo grego, idealista, fundador da Academia.", "atributos": {"Influência":100, "Originalidade":90, "Complexidade":85, "Longevidade":80}, "super_trunfo": True},
        {"nome":"Aristóteles", "descricao":"Aluno de Platão, grande sistematizador da filosofia e das ciências.", "atributos": {"Influência":98, "Originalidade":85, "Complexidade":88, "Longevidade":62}, "super_trunfo": False},
        {"nome":"Sócrates", "descricao":"Pai da ética ocidental; método socrático de perguntas.", "atributos": {"Influência":95, "Originalidade":92, "Complexidade":80, "Longevidade":70}, "super_trunfo": False},
        {"nome":"Santo Agostinho", "descricao":"Teólogo e filósofo cristão, importante na patrística.", "atributos": {"Influência":90, "Originalidade":75, "Complexidade":87, "Longevidade":75}, "super_trunfo": False},
        {"nome":"Tomás de Aquino", "descricao":"Integra fé e razão; escolástica medieval.", "atributos": {"Influência":92, "Originalidade":70, "Complexidade":90, "Longevidade":49}, "super_trunfo": False},
        {"nome":"René Descartes", "descricao":"Racionalista moderno; 'penso, logo existo'.", "atributos": {"Influência":94, "Originalidade":93, "Complexidade":88, "Longevidade":53}, "super_trunfo": False},
        {"nome":"Immanuel Kant", "descricao":"Crítico da razão pura; revolução na epistemologia.", "atributos": {"Influência":96, "Originalidade":90, "Complexidade":95, "Longevidade":79}, "super_trunfo": False},
        {"nome":"Friedrich Nietzsche", "descricao":"Crítico cultural radical; 'vontade de poder'.", "atributos": {"Influência":93, "Originalidade":97, "Complexidade":92, "Longevidade":55}, "super_trunfo": False},
        {"nome":"Jean-Paul Sartre", "descricao":"Existencialismo; liberdade e responsabilidade.", "atributos": {"Influência":89, "Originalidade":92, "Complexidade":90, "Longevidade":74}, "super_trunfo": False},
        {"nome":"Michel Foucault", "descricao":"Análises do poder, discurso e instituições.", "atributos": {"Influência":88, "Originalidade":90, "Complexidade":94, "Longevidade":57}, "super_trunfo": False},
        {"nome":"Karl Marx", "descricao":"Crítico do capitalismo; teoria materialista da história.", "atributos": {"Influência":97, "Originalidade":88, "Complexidade":91, "Longevidade":65}, "super_trunfo": False},
        {"nome":"Confúcio", "descricao":"Pensador chinês sobre ética, hierarquia social e virtude.", "atributos": {"Influência":95, "Originalidade":80, "Complexidade":85, "Longevidade":72}, "super_trunfo": False},
    ]

# --- Funções utilitárias ---
def shuffle_and_deal(deck):
    random.shuffle(deck)
    half = len(deck) // 2
    return deck[:half], deck[half:]

def show_card(card, ocultar_atributos=False):
    print(f"\n➡️ {card['nome']}{' (SUPER TRUNFO)' if card.get('super_trunfo') else ''}")
    print(textwrap.fill(card['descricao'], width=70))
    if not ocultar_atributos:
        print(" Atributos:")
        for k,v in card['atributos'].items():
            print(f"  - {k}: {v}")

def choose_attribute_by_cpu(card):
    # Estratégia simples: escolher o atributo de maior valor da carta da CPU
    atribs = card['atributos']
    escolha = max(at | {} for at in [ {k:v} for k,v in atribs.items() ])  # silly but ensures deterministic
    # melhor forma:
    best = max(atrituple for atrituple in atribs.items())[0]  # returns key of max by key (not value) -> fix below
    # Let's implement correctly:
    best_key = max(attribs.items(), key=lambda kv: kv[1])[0]
    return best_key

def resolve_round(card_p1, card_p2, atributo_escolhido, player_is_p1):
    # Checar Super Trunfo: Platão vence automaticamente se o outro não for Platão
    st1 = card_p1.get('super_trunfo', False)
    st2 = card_p2.get('super_trunfo', False)
    if st1 and not st2:
        return "player"
    if st2 and not st1:
        return "cpu"
    # Senão comparar atributo
    v1 = card_p1['atributos'][atributo_escolhido]
    v2 = card_p2['atributos'][atributo_escolhido]
    if v1 > v2:
        return "player"
    elif v2 > v1:
        return "cpu"
    else:
        return "tie"

def play_game():
    deck = create_deck()
    player_deck, cpu_deck = shuffle_and_deal(deck)
    round_counter = 0
    current_player_is_human = True  # quem escolhe atributo no início (alternamos no empate? manter humano inicia)
    print("=== Super Trunfo: Filósofos ===")
    print("Você joga contra a CPU. Platão é o Super Trunfo.\n")
    input("Pressione Enter para começar...")

    while player_deck and cpu_deck:
        round_counter += 1
        print("\n" + "="*40)
        print(f"Rodada {round_counter} — Cartas: Você {len(player_deck)} x CPU {len(cpu_deck)}")
        card_p = player_deck.pop(0)   # carta do jogador (topo)
        card_c = cpu_deck.pop(0)      # carta do cpu (topo)
        print("\nSua carta:")
        show_card(card_p)
        # Decide quem escolhe atributo: humano se current_player_is_human True
        if current_player_is_human:
            # pedir escolha do atributo
            atributos = list(card_p['atributos'].keys())
            print("\nEscolha um atributo para disputar:")
            for i, a in enumerate(atributos, start=1):
                print(f" {i}. {a} ({card_p['atributos'][a]})")
            # input com validação
            escolha_idx = None
            while escolha_idx is None:
                entrada = input("Digite o número do atributo (ou 'i' para ver a carta da CPU): ").strip().lower()
                if entrada == 'i':
                    print("\nCarta da CPU (exibindo descrição, atributos ocultos):")
                    show_card(card_c, ocultar_atributos=True)
                    continue
                if entrada.isdigit():
                    n = int(entrada)
                    if 1 <= n <= len(atributos):
                        escolha_idx = n-1
                        break
                print("Entrada inválida. Tente novamente.")
            atributo = atributos[escolha_idx]
        else:
            # CPU escolhe com estratégia
            atributo = choose_attribute_by_cpu(card_c)
            print(f"\nCPU escolheu disputar pelo atributo: {atributo}")

        # Mostrar a carta da CPU (com atributos)
        print("\nCarta da CPU:")
        show_card(card_c)

        # Resolver round
        resultado = resolve_round(card_p, card_c, atributo, player_is_p1=True)
        if resultado == "player":
            print(f"\n✅ Você venceu a rodada! ({atributo}: {card_p['atributos'][atributo]} x {card_c['atributos'][atributo]})")
            # vencedor recolhe as duas cartas (coloca no fim do deck)
            player_deck.extend([card_p, card_c])
            # quem vence escolhe atributo na próxima rodada
            current_player_is_human = True
        elif resultado == "cpu":
            print(f"\n❌ CPU venceu a rodada. ({atributo}: {card_p['atributos'][atributo]} x {card_c['atributos'][atributo]})")
            cpu_deck.extend([card_p, card_c])
            current_player_is_human = False
        else:  # empate
            print(f"\n🔁 Empate! ({atributo}: {card_p['atributos'][atributo]} x {card_c['atributos'][atributo]})")
            # no empate, cada um recolhe sua carta para o fim do respectivo deck
            player_deck.append(card_p)
            cpu_deck.append(card_c)
            # regra alternativa: o jogador que iniciou mantém escolha; aqui mantemos o current_player como estava
            # current_player_is_human permanece o mesmo

        # breve pausa controlada
        # permitir que usuário desista
        resposta = input("\nContinuar? (Enter para continuar, 'q' para sair): ").strip().lower()
        if resposta == 'q':
            print("Jogo finalizado pelo usuário.")
            break

    # resultado final
    print("\n" + "="*40)
    if not cpu_deck:
        print("🎉 Parabéns — você venceu o jogo! A CPU ficou sem cartas.")
    elif not player_deck:
        print("😵 A CPU venceu o jogo. Você ficou sem cartas.")
    else:
        print("Jogo encerrado antes do fim.")
    print(f"Rodadas jogadas: {round_counter}")
    print("Obrigado por jogar Super Trunfo: Filósofos!")

# --- Main ---
if _name_ == "_main_":
    try:
        play_game()
    except KeyboardInterrupt:
        print("\nJogo interrompido (CTRL+C). Até a próxima!")
        sys.exit(0)
