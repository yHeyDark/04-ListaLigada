# Guia de Atividade: Implementando uma Lista Ligada em C++

## Introdução: Por que usar uma Lista Ligada?

Imagine que você precisa criar uma lista de compras. Uma abordagem seria pegar uma folha de papel com um número fixo de linhas, digamos 20. Isso é como um **array**: tem um tamanho fixo. Mas e se você precisar de 21 itens? Ou se usar apenas 5? Você terá que pegar uma folha maior e copiar tudo, ou acabará desperdiçando papel.

Uma **lista ligada** é como usar notas adesivas (post-its). Cada item da sua lista vai em uma nota separada. Além do nome do item, você desenha uma seta que aponta para a próxima nota adesiva. Para adicionar um novo item, você só precisa de uma nova nota e de ajustar a seta do item anterior. Essa abordagem é flexível e só usa o espaço de que realmente precisa.

Nesta atividade, vamos construir essa "lista de notas adesivas" em C++, onde cada "nota" é um `nó` (struct `NO`) e cada "seta" é um `ponteiro`.

## Objetivos de Aprendizagem

Ao concluir esta atividade, você será capaz de:

  * **Definir e usar `structs`** para criar tipos de dados compostos.
  * **Manipular ponteiros** para acessar e conectar estruturas na memória.
  * **Utilizar os operadores `*` e `->`** corretamente no contexto de ponteiros para structs.
  * **Gerenciar memória dinamicamente** com `malloc` e `free`.
  * **Implementar as operações fundamentais** de uma lista ligada simples: inserção, busca e exclusão.

-----

## Revisão dos Conceitos Fundamentais

### 1\. `struct NO` - O Bloco de Construção (A Nota Adesiva)

Em C++, uma `struct` (estrutura) nos permite agrupar variáveis de diferentes tipos em uma única unidade. Para nossa lista, cada elemento, ou **nó**, precisa guardar duas informações:

1.  O dado em si (um número inteiro).
2.  A localização do próximo nó na sequência (a "seta").

<!-- end list -->

```cpp
struct NO {
    int valor;    // O dado que queremos armazenar.
    NO* prox;     // Um ponteiro para outro nó do mesmo tipo.
                  // É a "seta" que conecta este nó ao próximo.
};
```

Quando `prox` é `NULL`, significa que chegamos ao fim da lista.

### 2\. Ponteiros - O Endereço das Coisas

Pense na memória do computador como um gigantesco armário com milhões de gavetas, cada uma com um endereço único. Um **ponteiro** é uma variável especial que não guarda um valor comum (como o número 5 ou a letra 'a'), mas sim o **endereço de uma gaveta**.

No nosso código:

  * `NO* primeiro`: É um ponteiro que sempre guarda o endereço do primeiro nó da lista. Se a lista está vazia, ele aponta para `NULL`.
  * `NO* aux`: É um ponteiro temporário que usamos para "caminhar" pela lista, indo de nó em nó.
  * **Operador `->`**: É um atalho muito útil. Em vez de escrever `(*aux).valor` para acessar o campo `valor` do nó para o qual `aux` aponta, podemos simplesmente usar `aux->valor`.

### 3\. Alocação Dinâmica de Memória (`malloc` e `free`)

Como não sabemos quantos elementos o usuário irá inserir, não podemos reservar um espaço fixo na memória. Usamos a alocação dinâmica para pedir memória "sob demanda".

```cpp
// Pede ao sistema operacional espaço suficiente para armazenar um 'NO'.
NO* novo = (NO*) malloc(sizeof(NO));
```

  * `sizeof(NO)`: Calcula o tamanho em bytes de nossa `struct`.
  * `malloc`: Tenta reservar esse espaço na memória. Se conseguir, retorna o endereço do espaço alocado. Se não houver memória disponível, retorna `NULL`.
  * `free(ponteiro)`: Quando um nó não é mais necessário (por exemplo, após ser excluído), usamos `free` para devolver a memória ao sistema operacional, evitando "vazamentos de memória".

> **Nota C vs. C++**: No código fornecido, usamos `malloc` e `free`, que são herdados da linguagem C. Em C++ moderno, é mais comum e seguro usar os operadores `new` e `delete`. Eles funcionam de forma semelhante, mas são mais integrados à linguagem (por exemplo, chamam construtores e destrutores de objetos automaticamente). Para este exercício, vamos manter `malloc` e `free` para focar nos conceitos de ponteiros.

-----

## Analisando o Código-Fonte (`ListaLigada.cpp`)

Antes de começar, entenda a estrutura do código que você recebeu:

  * `NO* primeiro = NULL;`: Uma variável global que atua como ponto de entrada da nossa lista. Inicialmente, ela é `NULL`, indicando que a lista está vazia.
  * `menu()`: Controla a interação com o usuário, exibindo as opções e chamando as funções correspondentes.
  * `inicializar()`: Limpa a lista, liberando a memória de todos os nós existentes.
  * `exibir...()`: Funções para mostrar o conteúdo da lista e sua quantidade de elementos.
  * `inserirElemento()`: A versão atual insere um novo elemento no final da lista.
  * `posicaoElemento(int numero)`: **Esta é sua principal ferramenta\!** Ela já está pronta e faz o trabalho de percorrer a lista para encontrar um número. Ela retorna o **endereço do nó** se o encontrar, ou `NULL` caso contrário. Use-a para evitar reescrever a mesma lógica de busca várias vezes.

-----

## Sua Missão: Atividade Proposta

Faça um *fork* do repositório  e complete as tarefas abaixo.

### Tarefa 1: Impedir Valores Duplicados em `inserirElemento`

Modifique a função `inserirElemento`. Antes de adicionar o novo nó no final da lista, verifique se o valor digitado pelo usuário já existe.

  * **Dica:** Chame a função `posicaoElemento()` com o valor que se deseja inserir. Se o retorno for diferente de `NULL`, significa que o elemento já existe. Nesse caso, exiba uma mensagem ao usuário e não realize a inserção.

### Tarefa 2: Implementar a Função `buscarElemento`

Esta função deve permitir ao usuário verificar se um número está na lista.

1.  Peça para o usuário digitar o número que deseja buscar.
2.  Use a função `posicaoElemento()` para procurar por esse número.
3.  Se a função retornar um endereço (diferente de `NULL`), exiba a mensagem: `"ENCONTRADO"`.
4.  Se a função retornar `NULL`, exiba a mensagem: `"ELEMENTO NAO ENCONTRADO"`.

### Tarefa 3: Implementar a Função `excluirElemento`

Esta é a tarefa mais desafiadora. Não basta apenas encontrar o nó e usar `free`. Você precisa garantir que os ponteiros `prox` dos nós restantes continuem conectados corretamente.

**Lógica Geral:**

1.  Peça para o usuário digitar o número que deseja excluir.
2.  Use `posicaoElemento()` para verificar se o elemento existe. Se não existir, exiba `"ELEMENTO NAO ENCONTRADO"` e finalize.
3.  Se o elemento existe, você precisará tratar dois casos principais:
      * **Caso A: O elemento a ser excluído é o primeiro da lista (`primeiro`).**
          * Você precisa fazer o ponteiro `primeiro` apontar para o *segundo* elemento da lista (`primeiro->prox`).
          * Depois de atualizar o `primeiro`, você pode liberar a memória do nó que era o antigo primeiro.
      * **Caso B: O elemento está no meio ou no fim da lista.**
          * Para "religar" a lista, você precisa do endereço do nó **anterior** ao que será excluído.
          * Percorra a lista usando dois ponteiros: `NO* atual` e `NO* anterior`.
          * Quando `atual->valor` for o número a ser excluído, faça com que `anterior->prox` aponte para `atual->prox`. Isso efetivamente "pula" o nó `atual` da sequência.
          * Agora que o nó `atual` está isolado, libere sua memória com `free`.

-----

## Sugestão de Roteiro de Testes

Após implementar as funções, use o menu para testar exaustivamente sua lógica.

1.  **Lista Vazia**: Tente exibir, buscar e excluir elementos em uma lista vazia (opção 1).
2.  **Inserção**:
      * Insira os valores: 10, 20, 30. Exiba a lista para confirmar.
      * Tente inserir o valor 20 novamente. O programa deve impedir a duplicata.
3.  **Busca**:
      * Busque pelo valor 10 (existente). Deve retornar "ENCONTRADO".
      * Busque pelo valor 99 (inexistente). Deve retornar "ELEMENTO NAO ENCONTRADO".
4.  **Exclusão**:
      * **Excluir o primeiro:** Exclua o 10. Exiba a lista. Ela deve conter [20, 30].
      * **Excluir o último:** Insira 40. A lista é [20, 30, 40]. Exclua o 40. Exiba a lista. Ela deve conter [20, 30].
      * **Excluir do meio:** Exclua o 30. Exiba a lista. Ela deve conter [20].
5.  **Verificação Final**: Verifique se a quantidade de elementos é atualizada corretamente após cada inserção e exclusão.


## Checklist de Entrega

Antes de submeter sua atividade, verifique se você completou todos os itens abaixo.

  * **[ ]** A função `inserirElemento` foi alterada para não permitir a inserção de valores duplicados.
  * **[ ]** A função `buscarElemento` foi implementada e exibe a mensagem correta se o elemento é encontrado ou não.
  * **[ ]** A função `excluirElemento` foi implementada e trata corretamente a exclusão de um elemento no início, meio ou fim da lista.
  * **[ ]** O código foi compilado sem erros e testado, passando por todos os cenários sugeridos no roteiro de testes.
  * **[ ]** O código está salvo no seu repositório Git pessoal 

**Como entregar:**
Copie o link do seu repositório e cole-o na tarefa correspondente no Microsoft Teams para avaliação.

## Recursos Adicionais e Referências

  * **[Artigo] GeeksforGeeks - Linked List Data Structure**: Um dos melhores recursos online, com explicações detalhadas e exemplos de código para todas as operações.
      * [https://www.geeksforgeeks.org/data-structures/linked-list/](https://www.geeksforgeeks.org/data-structures/linked-list/)
  * **[Vídeo] Lista Encadeada - Estrutura de Dados (YouTube)**: Para quem prefere um aprendizado visual, este vídeo do canal "Programação Descomplicada" explica o conceito de forma clara (em português).
      * [https://www.youtube.com/watch?v=S5ZU8fErv\_E](https://www.google.com/search?q=https://www.youtube.com/watch%3Fv%3DS5ZU8fErv_E)
  * **[Documentação] cplusplus.com - Pointers**: Para revisar a sintaxe e o conceito de ponteiros em C++.
      * [https://cplusplus.com/doc/tutorial/pointers/](https://cplusplus.com/doc/tutorial/pointers/)
