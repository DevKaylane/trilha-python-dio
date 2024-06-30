import datetime
import textwrap
import re

menu = """
[d] Depositar
[s] Sacar
[e] Extrato
[nu] Novo Usuário
[nc] Nova Conta
[lc] Listar Contas
[q] Sair
=> """

class Conta:
    def __init__(self, agencia, numero_conta, usuario):
        self.agencia = agencia
        self.numero_conta = numero_conta
        self.usuario = usuario
        self.saldo = 0
        self.limite = 500
        self.extrato = []
        self.numero_saques = 0
        self.LIMITE_SAQUES = 3

 def depositar(self):
        valor = float(input("Informe o valor do depósito: "))
        if valor > 0:
            self.saldo += valor
            self.extrato.append((datetime.datetime.now(), "Depósito", valor))
            print("\n=== Depósito realizado com sucesso! ===")
        else:
            print("\n@@@ Operação falhou! O valor informado é inválido. @@@")

    def sacar(self):
        valor = float(input("Informe o valor do saque: "))
        excedeu_saldo = valor > self.saldo
        excedeu_limite = valor > self.limite
        excedeu_saques = self.numero_saques >= self.LIMITE_SAQUES

        if excedeu_saldo:
            print("\n@@@ Operação falhou! Você não tem saldo suficiente. @@@")
        elif excedeu_limite:
            print("\n@@@ Operação falhou! O valor do saque excedeu o limite. @@@")
        elif excedeu_saques:
            print("\n@@@ Operação falhou! Número máximo de saques excedidos. @@@")
        elif valor > 0:
            self.saldo -= valor
            self.extrato.append((datetime.datetime.now(), "Saque", valor))
            self.numero_saques += 1
            print("\n=== Saque realizado com sucesso! ===")
        else:
            print("\n@@@ Operação falhou! O valor informado é inválido. @@@")

    def exibir_extrato(self):
        print("\n================ EXTRATO ================")
        if not self.extrato:
            print("Não foram realizadas movimentações.")
        else:
            for data, operacao, valor in self.extrato:
                print(f"{data.strftime('%d/%m/%Y %H:%M:%S')} - {operacao}: R$ {valor:.2f}")
        print(f"\nSaldo:\t\tR$ {self.saldo:.2f}")
        print("==========================================")


def criar_usuario(usuarios):
  cpf = input("Informe o CPF (somente números): ")
  usuario = filtrar_usuario(cpf, usuarios)

  if usuario:
    print("\n@@@ Já existe usuário com esse CPF! @@@")
    return

  nome = input("Informe o nome completo: ")
  data_nascimento = input("Informe a data de nascimento (dd-mm-aaaa): ")
  endereco = input("Informe o endereço (logradouro, nro - bairro - cidade/sigla estado): ")

  usuarios.append(
    {
      "nome": nome,
      "data_nascimento": data_nascimento,
      "cpf": cpf,
      "endereco": endereco,
    }
  )
  print("=== Usuário criado com sucesso! ===")


def filtrar_usuario(cpf, usuarios):
  usuarios_filtrados = [usuario for usuario in usuarios if usuario["cpf"] == cpf]
  return usuarios_filtrados[0] if usuarios_filtrados else None


def criar_conta(agencia, numero_conta, usuarios):
  cpf = input("Informe o CPF do usuário: ")
  usuario = filtrar_usuario(cpf, usuarios)

  if usuario:
    print("\n=== Conta criada com sucesso! ===")
    return {
      "agencia": agencia,
      "numero_conta": numero_conta,      "usuario": usuario,
    }

  print("\n@@@ Usuário não encontrado, fluxo de criação de conta encerrado! @@@")
  return None # Retorna None se o usuário não for encontrado


def listar_contas(contas):
  for conta in contas:
    linha = f"""
Agência:\t{conta['agencia']}
C/C:\t\t{conta['numero_conta']}
Titular:\t{conta['usuario']['nome']}
"""
    print("=" * 100)
    print(textwrap.dedent(linha))

    def main():
    LIMITE_SAQUES = 3
    AGENCIA = "0001"

    usuarios = []
    contas = []

    while True:
        opcao = input(menu)

        if opcao == "d":
            cpf = input("Informe o CPF do titular da conta: ")
            conta = filtrar_usuario(cpf, contas)

            if conta:
                conta.depositar()  # Chama o método depositar da instância da conta
            else:
                print("\n@@@ Conta não encontrada! @@@")
        elif opcao == "s":
            cpf = input("Informe o CPF do titular da conta: ")
            conta = filtrar_usuario(cpf, contas)

            if conta:
                conta.sacar()  # Chama o método sacar da instância da conta
            else:
                print("\n@@@ Conta não encontrada! @@@")
        elif opcao == "e":
            cpf = input("Informe o CPF do titular da conta: ")
            conta = filtrar_usuario(cpf, contas)

            if conta:
                conta.exibir_extrato()  # Chama o método exibir_extrato da instância da conta
            else:
                print("\n@@@ Conta não encontrada! @@@")
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
            print("Operação inválida, por favor selecione novamente a operação desejada.")

if __name__ == "__main__":
    main()
