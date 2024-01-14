import textwrap

def menu():
    """Exibe o menu e retorna a escolha do usuário."""
    menu_text = """\n
    ========================  MENU ==================
    [d]\tDepositar
    [s]\tSacar
    [e]\tExtrato
    [nc]\tNova conta
    [lc]\tListar contas
    [nu]\tNovo usuário
    [q]\tSair
    """
    return input(textwrap.dedent(menu_text))

def depositar(saldo, valor, extrato):
    """Realiza um depósito na conta."""
    try:
        valor = float(valor)
    except ValueError:
        print("\n@@@ Operação falhou! Valor inválido. @@@")
        return saldo, extrato

    if valor > 0:
        saldo += valor
        extrato += f"Depósito:\tR$ {valor:.2f}\n"
        print("\n ===== Depósito realizado com sucesso =====")
    else:
        print("\n@@@ Operação falhou! O valor informado é inválido. @@@")
    return saldo, extrato

def sacar(*, saldo, valor, extrato, limite, numero_saques, limite_saques):
    """Realiza um saque na conta."""
    try:
        valor = float(valor)
    except ValueError:
        print("\n@@@ Operação falhou! Valor inválido. @@@")
        return saldo, extrato

    excedeu_saldo = valor > saldo
    excedeu_limite = valor > limite
    excedeu_saques = numero_saques >= limite_saques

    if excedeu_saldo:
        print("\n@@@ Operação falhou! Você não tem saldo suficiente. @@@")
    elif excedeu_limite:
        print("\n@@@ Operação falhou! O valor do saque excede o valor limite diario. @@@")
    elif excedeu_saques:
        print("\n@@@ Operação falhou! Número de saques excedido. @@@")
    elif valor > 0:
        saldo -= valor
        extrato += f"Saque:\t\tR$ {valor:.2f}\n"
        numero_saques += 1
        print("\n ==== Saque realizado com sucesso! ====")
    else:
        print("\n@@@ Operação falhou! O valor informado é inválido. @@@")
    return saldo, extrato

def exibir_extrato(saldo, *, extrato):
    """Exibe o extrato da conta."""
    print("\n============= EXTRATO ===========")
    print("Não foram realizadas movimentações." if not extrato else extrato)
    print(f"\nSaldo: \t\tR$ {saldo:.2f}")
    print("===================================")

def criar_usuario(usuarios):
    """Cria um novo usuário."""
    cpf = input("Informe o CPF (somente números): ")
    usuario = filtrar_usuario(cpf, usuarios)

    if usuario:
        print("\n@@@ CPF JÁ CADASTRADO! @@@")
        return

    nome = input("Informe o nome completo: ")
    data_nascimento = input("Informe a data de nascimento (dd-mm-aaaa): ")
    endereco = input("Informe o endereço (logradouro, nro - bairro - cidade/sigla estado): ")

    usuarios.append({"nome": nome, "data_nascimento": data_nascimento, "cpf": cpf, "endereco": endereco})
    print("=== Usuário criado com sucesso! ===")

def filtrar_usuario(cpf, usuarios):
    """Filtra um usuário pelo CPF."""
    usuarios_filtrados = [usuario for usuario in usuarios if usuario["cpf"] == cpf]
    return usuarios_filtrados[0] if usuarios_filtrados else None

def criar_conta(agencia, numero_conta, usuarios):
    """Cria uma nova conta."""
    cpf = input("Informe o CPF do usuário: ")
    usuario = filtrar_usuario(cpf, usuarios)

    if usuario:
        print("\n=== Conta criada com sucesso! ===")
        return {"agencia": agencia, "numero_conta": numero_conta, "usuario": usuario}
    else:
        print("\n@@@ Usuário não encontrado, página encerrada! @@@")
        return None

def listar_contas(contas):
    """Lista as contas existentes."""
    for conta in contas:
        linha = f"""\
            Agência:\t{conta['agencia']}
            C/C:\t\t{conta['numero_conta']}
            Titular:\t{conta['usuario']['nome']}
"""
        print("=" * 100)
        print(textwrap.dedent(linha))

def main():
    LIMITE_SAQUES = 3
    AGENCIA = "0001"

    saldo = 0
    limite = 500
    extrato = ""
    numero_saques = 0
    usuarios = []
    contas = []

    while True:
        opcao = menu()

        if opcao == "d":
            valor = input("Informe o valor do depósito: ")
            saldo, extrato = depositar(saldo, valor, extrato)

        elif opcao == "s":
            valor = input("Informe o valor do saque: ")
            saldo, extrato = sacar(
                saldo=saldo,
                valor=valor,
                extrato=extrato,
                limite=limite,
                numero_saques=numero_saques,
                limite_saques=LIMITE_SAQUES
            )

        elif opcao == "e":
            exibir_extrato(saldo, extrato=extrato)

        elif opcao == "nu":
            criar_usuario(usuarios)

        elif opcao == "nc":
            numero_conta = len(contas) + 1
            conta = criar_conta(AGENCIA, numero_conta, usuarios)

            if conta:
                contas.append(conta)

        elif opcao == "lc":
            listar_contas(contas)

        elif opcao == "q":
            break

        else:
            print("Operação inválida, por favor selecione novamente a operação desejada")

if __name__ == "__main__":
    main()
