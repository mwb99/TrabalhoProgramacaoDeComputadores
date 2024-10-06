#Perguntar o que o cliente quer fazer dentre as quatro opções: cadastrar, comprar, relatorio csv ou reatório txt
#LISTA para cadastrar perdutos e com código, nome, quantidade e valor.
#LISTA REGISTRO das vendas com verificação de estoque disponível. - código do produto, quantidade vendida
#RELATORIO DE VENDA (CSV) - código, nome, quantidade, valor total da venda
#RELATORIO DE ESTOQUE(GERAÇÃO DE ARQUIVO TXT) - código do produto, nome do produto e quatidade do estoque

import csv
import pandas as pd

produtos = {}
vendas = []

# Sistema de cadastro de produtos.
def Cadastrar_Produtos():
    while True: 
        codigo = input("Digite o código do produto: ")
        nome = input("Digite o nome do produto: ")

        # Adicionando validação de entrada de quantidade e preço
        while True:
            try:
                quantidade = int(input("Digite a quantidade do produto: "))
                break
            except ValueError:
                print("Erro: A quantidade deve ser um número inteiro.")
        
        while True:
            try:
                valor = float(input("Digite o valor do produto: "))
                break
            except ValueError:
                print("Erro: O valor deve ser um número válido.")
        
        produtos[codigo] = {
            "codigo": codigo,
            "nome" : nome,
            "quantidade": quantidade,
            "valor" : valor
        }
        
        verificar = input("Deseja cadastrar outro produto? (s/n): ")
        if verificar.lower() != 's':  # Case sensitive
            break

# Sistema de registro de venda.
def Registrar_Venda():
    while True:
        codigo = input("Digite o código do produto para registrar a venda: ")
        if codigo not in produtos:
            print("Erro: Esse código de produto não está cadastrado.")
            continue
        
        while True:
            try:
                quantidadeVendida = int(input("Digite a quantidade vendida desse produto: "))
                if quantidadeVendida > produtos[codigo]["quantidade"]:
                    print(f"Erro: Não pode vender mais do que o estoque. Estoque disponível: {produtos[codigo]['quantidade']}")
                else:
                    break
            except ValueError:
                print("Erro: A quantidade vendida deve ser um número inteiro.")
                
        produto = produtos[codigo]
        produto["quantidade"] -= quantidadeVendida
        vendas.append({
            "codigo" : codigo,
            "nome": produto["nome"],
            "quantidadeVendida": quantidadeVendida,
            "valorTotal" : quantidadeVendida * produto["valor"]
        })
        print(f'Venda registrada: {quantidadeVendida} unidades de {produto["nome"]}')
        
        verificar = input("Deseja registrar outra venda? (s/n): ")
        if verificar.lower() != 's':  # Case sensitive
            break

# Sistema de geração de relatório de vendas.
def Gerar_Relatorio_Vendas(arquivo_csv):
    try:
        with open(arquivo_csv, mode='w', newline='') as arquivo:
            fieldnames = ["codigo", "nome", "quantidadeVendida", "valorTotal"]
            writer = csv.DictWriter(arquivo, fieldnames=fieldnames, delimiter=';')
            writer.writeheader()
            for venda in vendas:
                writer.writerow({
                    "codigo": venda["codigo"],
                    "nome": venda["nome"],
                    "quantidadeVendida": venda["quantidadeVendida"],
                    "valorTotal": venda["valorTotal"]
                })
        print(f'Relatório de vendas gerado com sucesso: {arquivo_csv}')
    except IOError:
        print(f"Erro: Não foi possível escrever o arquivo {arquivo_csv}")

# Sistema de relatório de estoque
def Gerar_Relatorio_Estoque(arquivo_txt):
    try:
        with open(arquivo_txt, 'w') as arquivo:
            for codigo, produto in produtos.items():
                arquivo.write(f'Código: {codigo}, Nome: {produto["nome"]}, Quantidade: {produto["quantidade"]}\n')
        print(f'Relatório de estoque gerado com sucesso: {arquivo_txt}')
    except IOError:
        print(f"Erro: Não foi possível escrever o arquivo {arquivo_txt}")

# Inicia o programa chamando o menu.
def Exibir_Menu():
    while True:
        print("\n--- Menu ---")
        print(f"Bem vindo(a) ao sistema de cadastro e registro de vendas!")
        print("1. Cadastrar produtos")
        print("2. Registrar venda")
        print("3. Gerar relatório de vendas (CSV)")
        print("4. Gerar relatório de estoque (TXT)")
        print("5. Sair")
        
        opcao = input("Escolha uma opção: ")
        
        if opcao == '1':
            Cadastrar_Produtos()
        elif opcao == '2':
            Registrar_Venda()
        elif opcao == '3':
            Gerar_Relatorio_Vendas('vendas.csv')
        elif opcao == '4':
            Gerar_Relatorio_Estoque('estoque.txt')
        elif opcao == '5':
            print("Saindo...")
            break
        else:
            print("Opção inválida, por favor escolha uma opção válida.")

# Chama o menu para iniciar o programa
Exibir_Menu()
