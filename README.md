#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <time.h>
int opcao;

// estrutura básica
typedef struct {
  char nome[30];
  char codigo;
  int quantidade_disponivel;
  int n_vendas;
  int n_compras;
  float preco;
  int status;
} Produto;

// buscar
// A busca do produto dever ser por código e deve retornar o índice do produto
// encontrado. Caso não encontre, deve retornar -1.

int buscar(Produto *produtos, int *posicao, char codigo[]) {
  int produtosEncontrado = 0;

  for (int i = 0; i < *posicao; i++) {
    if (strcmp(produtos[i].codigo, codigo) == 0) {
      return 1;
    }
  }
  printf("Produto não encontrado\n");
  return 0;
}

// inserir
// Não pode inserir produto com mesmo código. Todo produto criado deve ter
// “quantidade_disponível” igual a zero e “status” igual a 1. A função de
// inserção deve retornar um valor 1, caso seja um sucesso, e 0, caso seja um
// insucesso.

int inserir(Produto *produtos, int *tamanho, int *posicao, Produto novo) {
  int a = buscar(produtos, posicao, novo.codigo);
  printf("\n%i", a);

  if (*posicao == *tamanho - 1) {
    produtos =
        (Produto *)realloc(produtos, (sizeof(produtos)) * (*tamanho + 2));
    tamanho += 2;
  }

  *posicao += 1;

  strcpy(produtos[*posicao].nome, novo.nome);
  produtos[*posicao].codigo = novo.codigo;
  produtos[*posicao].preco = novo.preco;
  produtos[*posicao].n_vendas = 0;
  produtos[*posicao].n_compras = 0;

  novo.quantidade_disponivel = 0;
  novo.status = 1;

  if (*posicao < 0 || *posicao > *tamanho) {
    printf("Erro ao inserir produto");
    return 0;
  } else {
    produtos[*posicao] = novo;
    return 1;
  }
}

// remover
// Não pode remover um produto que não existe. Caso o produto exista, deve-se
// alterar o “status” para 0. A função de remoção deve retornar um valor 1, caso
// seja um sucesso, e 0, caso seja um insucesso.
int remover(Produto *produtos, int *posicao, char codigo[]) {
  for (int i = 0; i < *posicao; i++) {
    if (strcmp(produtos[i].codigo, codigo) == 0) {
      produtos[i].status = 0;
      printf("Produto de código %i não encontrado\n", codigo);
      return 0;
    } else {
      printf("Produto removido\n");
      return 1;
    }
  }
}

// comprar
// Deve-se verificar se o produto com o “codigo” existe. Se existir, deve-se
// incrementar a “quantidade_disponivel” e “n_compras”,e retornar 1 para
// informar o sucesso. Caso contrário, deve-ser retornar 0. Não pode-se comprar
// um produto removido (status igual a 0).
int comprar(Produto *produtos, int *posicao, char codigo[]) {
  for (int i = 0; i < *posicao; i++) {
    if (strcmp(produtos[i].codigo, codigo) == 0 && produtos[i].status == 1) {
      produtos[i].quantidade_disponivel++;
      produtos[i].n_compras++;
      printf("Compra realizada com sucesso\n");
      return 1;
    }
  }
  printf("Produto não encontrado\n");
  return 0;
}

// vender
// Deve-se verificar se o produto com o “codigo” existe. Se existir e a
// “quantidade_disponivel” for maior que zero, deve-se decrementar a
// “quantidade_disponivel” e incrementar o “n_vendas”, e retornar 1 para
// informar o sucesso. Caso contrário, deve-ser retornar 0. Não pode-se vender
// um produto removido (status igual a 0).
int vender(Produto *produtos, int *posicao, char codigo[]) {
  for (int i = 0; i < *posicao; i++) {
    if (strcmp(produtos[i].codigo, codigo) == 0 && produtos[i].status == 1 &&
        produtos[i].quantidade_disponivel > 0) {
      produtos[i].quantidade_disponivel--;
      produtos[i].n_vendas++;
      printf("Venda realizada com sucesso\n");
      return 1;
    }
  }
  printf("Produto não encontrado\n");
  return 0;
}

// relatorio
int relatorio_extrato(Produto *produtos, int *posicao) {
  for (int i = 0; i < *posicao; i++) {
    printf("Produto: %s, comprado:%d, vendido:%d, preço:%f\n", produtos[i].nome,
           produtos[i].n_compras, produtos[i].n_vendas);
  }
}

void menu() {
  printf("Menu:\n1-Inserir produto\n2-Remover produto\n3-Buscar "
         "produto\n4-Comprar produto\n5-Vender produto\n6-Imprimir "
         "relatório\n7-Sair\n");
  printf("\nDigite a opção que você deseja: ");
  scanf("%i", &opcao);
  getchar();
}

int main(void) {

  int tamanho = 10;
  Produto *produtos;
  produtos = (Produto *)malloc(sizeof(Produto) * tamanho);
  Produto novo;
  int posicao = 0;
  int codigo;
  int opcao;
  int continuar = 1;
  int status = 0;

  menu();

  while (continuar == 1) {
    switch (opcao) {

    case 1:
      printf("\nDigite o nome do produto: \n");
      scanf("%s", novo.nome);
      getchar();

      printf("\nDigite o código do produto: \n");
      scanf("%i", &novo.codigo);
      getchar();

      printf("\nDigite o preço do produto: \n");
      scanf("%f", &novo.preco);
      getchar();

      opcao = inserir(produtos, &tamanho, &posicao, novo);

      if (opcao == 1) {
        printf("\nProduto inserido com sucesso!\n");
      } else {
        printf("\nProduto não inserido\n");
      }
      break;

    case 2:
      printf("\nDigite o código do produto que você deseja remover: \n");
      scanf("%i", &codigo);
      getchar();

      opcao = buscar(produtos, &tamanho, codigo);

      if (opcao == 1) {
        printf("\nProduto removido %i com sucesso!\n", codigo);
      } else {
        printf("\nProduto não encontrado\n");
      }
      break;

    case 3:
      printf("\nDigite o código do produto que você deseja buscar: \n");
      scanf("%i", &codigo);
      getchar();

      opcao = buscar(produtos, &tamanho, codigo);

      if (opcao == 1) {
        printf("\nProduto encontrado %i com sucesso!\n", codigo);
      } else {
        printf("\nProduto %i não encontrado\n", codigo);
      }
      break;

    case 4:
      printf("\nDigite o código do produto que você deseja comprar: \n");
      scanf("%i", &codigo);
      getchar();

      opcao = buscar(produtos, &tamanho, codigo);

      if (opcao == 1) {
        printf("\nProduto %i comprado com sucesso!\n", codigo);
      } else {
        printf("\nProduto %i não encontrado\n", codigo);
      }
      break;

    case 5:
      printf("\nDigite o código do produto que você deseja vender: \n");
      scanf("%i", &codigo);
      getchar();

      opcao = buscar(produtos, &tamanho, codigo);

      if (opcao == 1) {
        printf("\nProduto %i vendido com sucesso!\n", codigo);
      } else {
        printf("\nProduto %i não encontrado\n", codigo);
      }
      break;

    case 6:
      printf("\nRelatório\n");
      break;

    case 7:
      printf("\nSaindo...\n");
      continuar = 0;
      break;

    default:
      printf("Inválido");
    }
  }

  return 0;
}
