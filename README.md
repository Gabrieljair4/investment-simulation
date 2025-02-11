# investment-simulation
Este projeto em Python é um simulador de investimentos que utiliza a biblioteca yfinance para obter dados históricos de ativos e calcular a rentabilidade média. O simulador permite que o usuário defina uma carteira de investimentos, aloque porcentagens para cada ativo, e simule a evolução do capital ao longo do tempo com aportes periódicos.

import yfinance as yf
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

def obter_rentabilidade(ativo, periodo):
    try:
        dados = yf.Ticker(ativo).history(period=periodo)
        retornos = dados['Close'].pct_change().dropna()
        return retornos.mean()
    except Exception as e:
        print(f"Erro ao obter dados de {ativo}: {e}")
        return None

def simular_investimentos(ativos, alocacao, aporte_inicial, aporte_periodico, meses, periodo_dados):
    capital = aporte_inicial
    historico = []
    rentabilidades = {ativo: obter_rentabilidade(ativo, periodo_dados) for ativo in ativos}
    
    for mes in range(1, meses + 1):
        capital += aporte_periodico
        rendimento_mensal = sum(alocacao[ativo] * rentabilidades.get(ativo, 0) for ativo in ativos)
        capital *= (1 + rendimento_mensal)
        historico.append(capital)
    
    return historico

def plotar_resultados(historico):
    plt.figure(figsize=(10, 5))
    plt.plot(historico, label="Evolução da Carteira")
    plt.xlabel("Meses")
    plt.ylabel("Capital (R$)")
    plt.legend()
    plt.grid()
    plt.show()

def main():
    ativos = input("Digite os ativos separados por vírgula (ex: PETR4.SA,VALE3.SA,ITUB4.SA): ").split(',')
    alocacao = {ativo: float(input(f"Porcentagem para {ativo}: ")) / 100 for ativo in ativos}
    aporte_inicial = float(input("Valor do aporte inicial: "))
    aporte_periodico = float(input("Valor dos aportes periódicos: "))
    meses = int(input("Período total em meses: "))
    periodo_dados = "1y"  # Pega a rentabilidade média do último ano
    
    historico = simular_investimentos(ativos, alocacao, aporte_inicial, aporte_periodico, meses, periodo_dados)
    plotar_resultados(historico)

if __name__ == "__main__":
    main()
