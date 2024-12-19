
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <time.h>
#define MAX_CLIENTES 100
#define MAX_TRANSACOES 100
typedef struct
{
	char descricao[50];
	float valor;
	char data[11]; // formato: DD/MM/AAAA
} Transacao;

typedef struct
{
	int numeroConta;
	float saldo;
	Transacao historico[MAX_TRANSACOES];
	int qtdTransacoes;
} Conta;
typedef struct
{
	char nome[100];
	char documento[20];
	char endereco[200];
	char dataNascimento[11]; // formato: DD/MM/AAAA
	Conta conta;
} Cliente;
int verificaIdade(const char *dataNascimento)
{
	struct tm hoje = {0};
	time_t t = time(NULL);
	localtime_r(&t, &hoje);

	int dia, mes, ano;
	sscanf(dataNascimento, "%d/%d/%d", &dia, &mes, &ano);

	int idade = hoje.tm_year + 1900 - ano;
	if ((hoje.tm_mon + 1 < mes) || (hoje.tm_mon + 1 == mes && hoje.tm_mday < dia))
	{
		idade--;
	}
	return idade >= 18;
}

void cadastrarCliente(Cliente clientes[], int *qtdClientes)
{
	if (*qtdClientes >= MAX_CLIENTES)
	{
		printf("Limite de clientes atingido!\n");
		return;
	}

	Cliente novoCliente;
	printf("Nome: ");
	fgets(novoCliente.nome, 100, stdin);
	strtok(novoCliente.nome, "\n");

	printf("Documento: ");
	fgets(novoCliente.documento, 20, stdin);
	strtok(novoCliente.documento, "\n");

	printf("Endereco: ");
	fgets(novoCliente.endereco, 200, stdin);
	strtok(novoCliente.endereco, "\n");

	printf("Data de nascimento (DD/MM/AAAA): ");
	fgets(novoCliente.dataNascimento, 11, stdin);
	strtok(novoCliente.dataNascimento, "\n");

	if (!verificaIdade(novoCliente.dataNascimento))
	{
		printf("Cliente deve ser maior de 18 anos.\n");
		return;
	}
	novoCliente.conta.numeroConta = 1000 + *qtdClientes;
	novoCliente.conta.saldo = 0.0;
	novoCliente.conta.qtdTransacoes = 0;

	clientes[*qtdClientes] = novoCliente;
	(*qtdClientes)++;
	printf("Cliente cadastrado com sucesso! Numero da conta: %d\n", novoCliente.conta.numeroConta);
}
void depositar(Conta *conta, float valor)
{
	if (valor <= 0)
	{
		printf("Valor invalido.\n");
		return;
	}
	conta->saldo += valor;

	Transacao transacao = {"Deposito", valor, ""};
	snprintf(transacao.data, 11, "%02d/%02d/%04d", 1, 1, 2024); // Substituir por data real
	conta->historico[conta->qtdTransacoes++] = transacao;

	printf("Deposito realizado. Saldo atual: %.2f\n", conta->saldo);
}
void sacar(Conta *conta, float valor)
{
	if (valor <= 0 || valor > conta->saldo)
	{
		printf("Saldo insuficiente ou valor invalido.\n");
		return;
	}
	conta->saldo -= valor;

	Transacao transacao = {"Saque", -valor, ""};
	snprintf(transacao.data, 11, "%02d/%02d/%04d", 1, 1, 2024); // Substituir por data real
	conta->historico[conta->qtdTransacoes++] = transacao;

	printf("Saque realizado. Saldo atual: %.2f\n", conta->saldo);
}
void transferir(Conta *origem, Conta *destino, float valor)
{
	if (valor <= 0 || valor > origem->saldo)
	{
		printf("Saldo insuficiente ou valor invalido.\n");
		return;
	}
	origem->saldo -= valor;
	destino->saldo += valor;
	Transacao transacaoOrigem = {"Transferencia Enviada", -valor, ""};
	snprintf(transacaoOrigem.data, 11, "%02d/%02d/%04d", 1, 1, 2024);
	origem->historico[origem->qtdTransacoes++] = transacaoOrigem;
	Transacao transacaoDestino = {"Transferencia Recebida", valor, ""};
	snprintf(transacaoDestino.data, 11, "%02d/%02d/%04d", 1, 1, 2024);
	destino->historico[destino->qtdTransacoes++] = transacaoDestino;
	printf("Transferencia realizada. Saldo atual da conta origem: %.2f\n", origem->saldo);
}
void gerarExtrato(Conta conta)
{
	printf("Extrato Bancario - Conta %d\n", conta.numeroConta);
	printf("Saldo inicial: %.2f\n", conta.saldo);

	for (int i = 0; i < conta.qtdTransacoes; i++)
	{
		printf("[%s] %s: %.2f\n", conta.historico[i].data, conta.historico[i].descricao, conta.historico[i].valor);
	}
	printf("Saldo final: %.2f\n", conta.saldo);
}
int main()
{
	Cliente clientes[MAX_CLIENTES];
	int qtdClientes = 0;
	int opcao;

	do
	{
		printf("\nSistema de Gerenciamento Bancario\n");
		printf("1. Cadastrar cliente\n");
		printf("2. Depositar\n");
		printf("3. Sacar\n");
		printf("4. Transferir\n");
		printf("5. Gerar extrato\n");
		printf("6. Sair\n");
		printf("Opcao: ");
		scanf("%d", &opcao);
		getchar(); // limpar buffer

		switch (opcao)
		{
		case 1:
			cadastrarCliente(clientes, &qtdClientes);
			break;
		case 2:
			// Implementar lógica para buscar cliente e depositar
			break;
		case 3:
			// Implementar lógica para buscar cliente e sacar
			break;
		case 4:
			// Implementar lógica para transferir
			break;
		case 5:
			// Implementar lógica para gerar extrato
			break;
		case 6:
			printf("Encerrando o sistema.\n");
			break;
		default:
			printf("Opcao invalida.\n");
		}
	} while (opcao != 6);

	return 0;
}