# Árvores binárias de busca

Uma árvore binária de busca(BST) é uma árvore binária(cada nodo pode ter no máximo dois filhos) que satisfaz a seguinte propriedade: para todo nodo,o valor de todos os filhos na subárvore esquerda é menor que o valor do próprio nodo, e o valor de todos os filhos na subárvore direita é maior que o do próprio nodo. Aqui há uma demonstração visual de como elas funcionam: https://visualgo.net/en/bst

Essa definição é bastante abrangente, por esse motivo, existem diversos tipos de BSTs: (Splay, Scapegoat, Treap, AVL, Red-Black...)

## Set

Set é mais uma estrutura de dados da STL C++, que representa um conjunto matemático. Sua implementação é feita por uma BST, mais especificamente a AVL(se balanceia sozinho).

### declaração

Assim como no vector, temos que indicar qual de qual tipo será nosso set.

```cpp
set<int> conj;
```

### insert

para inserir no set, utiliza-se o método insert(). A complexidade dessa operação é ```O(log n)```
```cpp
set<int> conj;
conj.insert(1);
conj.insert(1);
conj.insert(2);

```

Assim como no conjunto matemático, valores duplicados não contam. Então nosso set teria os valores {1,2} após a execução desse trecho.



###  erase

Para retirar elementos do set, utiliza-se o método erase(). A complexidade dessa operação é ```O(log n)```

```cpp
set<int> conj;
conj.insert(1);
conj.insert(1);
conj.erase(1);
```

Ao fim da execução desse trecho, o conjunto está vazio.

### count

Pode-se verificar se um elemento está no set usando o método count(). A complexidade é ```O(log n)```

```cpp
set<int> conj;
conj.insert(1);
if(conj.count(1)){// retorna 1 se tiver o elemento
	// faça algo
}

```
### size

O método size retorna o tamanho do set, a complexidade é ```O(1)```
```cpp
set<int> conj;
conj.insert(1);
conj.insert(1);
conj.insert(2);
conj.insert(3);
int x = conj.size(); // x = 3

```

### clear()

Se você quiser reinicializar o set, pode usar o método clear(). Sua complexidade é ```O(n)```, já que precisa-se liberar toda memória alocada.




## Map

Map é outra estrutura de dados da STL C++, também implementada usando BSTs. Enquanto o set é análogo à um conjunto matemático, o map é análogo a uma função, no sentido que podemos mapear o retorno de uma determinada entrada. 

Para efeitos práticos, o map é bem parecido com set, mas ele permite guardar mais informações além de se o elemento está lá ou não. Maps nos permitem atrelar um dado a aquele elemento.

Mais formalmente, maps armazenam informações do tipo {chave, valor}, aonde os tipos de chave e de valor são definidos na declaração.


### Declaração
```cpp
// meu mapa recebe chaves do tipo string e armazena valores inteiros
map<string,int> meu_mapa;
// o acesso é como o de um vetor, mas o índice tem o tipo da chave.

mapa["Deivis"] = 2;

cout << mapa["Sei la"] << endl;
// Isso vai imprimir 0, as chaves que ainda nao tem valores sao inicializados com um valor base quando são acessadas.
```


### size

O método size retorna a quantidade de chaves criadas no map. No caso do map, é necessário tomar cuidado pois apenas acessar o valor de uma chave faz com que ela seja criada. A complexidade é ```O(1)```

```cpp
map<string, int> meu_mapa;
int x = meu_mapa["Deivis"];
int size = meu_mapa.size(); // size = 1
```

### count

Assim como no set, usa-se count para ver se uma chave está no map ou não, a função retorna 1 caso esteja. A complexidade é ```O(log n)```.

```cpp
	map<char,int> meu_mapa;
	meu_mapa['c'] = 2; 
	if(meu_mapa.count('c')) 
		puts("Meu mapa contém o char c");
```


### erase

Remove uma chave do map. A complexidade é ```O(log n)```
```cpp
	map<char,int> meu_mapa;
	meu_mapa['c'] = 2; 
	meu_mapa.erase('c');
	if(mp.count('c')) 
		puts("Meu mapa contem o char c");
	else 
		puts("Meu mapa nao contem o char c");
```

### clear()
Limpa o map. A complexidade é ```O(n)```.
```cpp
	map<char,int> mp;

	for(char i = 'd'; i < 'j'; i++){
		mp[i]++;
	}

	for(char i = 'a'; i < 'l'; i++){
		if(mp.count(i)) printf("%c pertence\n",i);
		else printf("%c NAO pertence\n",i);
	}
	puts("");
	mp.clear();
	
	for(char i = 'a'; i < 'l'; i++){
		if(mp.count(i)) printf("%c pertence\n",i);
		else printf("%c NAO pertence\n",i);
	}
```

## Percorrendo Map e Set





## Define

Defines são expressões pré definidas pelo programador, que podem facilitar sua vida reduzindo tempo de escrita. *MUITO* cuidado!!! Elas podem  acabar atrapalhando caso usadas de maneira errada.
```cpp
#include <bits/stdc++.h>
using namespace std;

#define pb push_back
#define mp make_pair
#define ii pair<int,int>
#define N 100000
int main(){

	vector<int> v;
	v.pb(N); // inserindo 100000 no vetor
	vector<ii> v2; //vetor de <int,int>
	v2.pb(mp(1,0)); //inserindo o par (1,0) no vetor de par
}
```