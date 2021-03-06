

# Decomposição em blocos

Consiste em dividir uma parte do problema em blocos menores de forma a atingir uma melhor complexidade.

## Soma de intervalos com update
Já vimos uma forma de computar a soma de intervalos quando não há updates usando preffix sums. Uma forma de resolver o problema tendo que efetuar updates além responder às queries seria guardar um vetor com todos os updates já feitos, usando-o para responder às queries apropriadamente.

```cpp
struct mystruct{ int before, pos, after; };
// Dentro da main lê n, q e o vetor v
build_preffix_sum(); // Monta a preffix sum com base no vetor v de tamanho n
vector<mystruct> updates;
for(int i = 0; i < q; i++){ // para toda query
  scanf("%d", &type);
  if(type == 0){ // caso seja um update
    scanf("%d %d", &j, &x); // pos j muda o valor para x
    updates.emplace_back(v[j], j, x);
    v[j] = x;
  }
  else{ // caso seja uma query
    scanf("%d %d", &l, &r);
    ll ans = query(l, r);
    for(auto up : updates){
      if(up.pos >= l && up.pos <= r){
        ans = ans - up.before + up.after;
      }
    }
    printf("%lld\n", ans);
  }
}
```

Como no código acima precisamos olhar o resultado de todo update anterior para resolver uma query, a complexidade é `O(n+q^2)`.

Podemos mudar drasticamente a complexidade com uma ideia simples. Definimos um número B e a cada B updates remontamos completamente a estrutura de preffix sum.

```cpp
struct mystruct{ int before, pos, after; };
// Dentro da main lê n e q, o vetor v
build_preffix_sum(); // Monta a preffix sum com base no vetor v de tamanho n
vector<mystruct> updates;
for(int i = 0; i < q; i++){ // para toda query
  scanf("%d", &type);
  if(type == 0){ // caso seja um update
    scanf("%d %d", &j, &x); // pos j muda o valor para x
    updates.emplace_back(v[j], j, x);
    v[j] = x;
    //-------Nova parte------
    if(updates.size() == B){
      build_preffix_sum();
      updates.clear();
    }
    //---Fim da nova parte---
  }
  else{ // caso seja uma query
    scanf("%d %d", &l, &r);
    ll ans = query(l, r);
    for(auto up : updates){
      if(up.pos >= l && up.pos <= r){
        ans = ans - up.before + up.after;
      }
    }
    printf("%lld\n", ans);
  }
}
```
BOOM! A magia está feita, com apenas 1 `if` e duas linhas dentro.

Olhando só para a parte da query, agora vemos que o vetor `updates` tem tamanho máximo B logo a complexidade `O(q*B)`. E olhando só para a parte do update, a cada B updates a preffix sum é remontada portanto complexidade `O(q/B*n)`. Num total de `O(q*B + q/B*n)`. Só nos resta escolher um bom valor para o parametro B, se escolhermos `B = q/2` por exemplo teríamos complexidade `O(q^2)` para executar as queries o que não melhora em nada em relação ao que começamos. Por outro lado, `B = sqrt(q)` nos serve bem ficando com complexidade `O((q+n)*sqrt(q))`, poderíamos também escolher `B = sqrt(n)` obtendo `O((q+n)*sqrt(n))`.

### Uma outra abordagem
Podemos aplicar a ideia de blocos de outra forma, dividimos o vetor em blocos de tamanho `B`, a não ser o último que pode ter o tamanho menor. Desta vez não usamos preffix sum, somente uma variável por bloco que indica o valor da soma dos elementos dentro dele.

Com indexamento do vetor e dos blocos a partir do 0, o bloco 0 cobrirá o intervalo `[0, B)`, o bloco 1 cobrirá o intervalo `[B, 2*B)`, o bloco 2 cobrirá o intervalo `[2*B, 3B)` e assim por diante. O elemento no indice `i` estará dentro do bloco de indice `i/B`, arredondado para baixo. Podemos ainda deduzir que os indices cujo resto por B sejam 0 (0, B, 2\*B, 3\*B ...), serão os índices de começo de um bloco.  

O código para resolver o problema fica:
```cpp
// Lê n e q, o vetor v
for(int i = 0; i < n; i += B)
  build_block(i / B);

for(int i = 0; i < q; i++){ // para toda query
  scanf("%d", &type);
  if(type == 0){ // caso seja um update
    scanf("%d %d", &j, &x); // pos j muda o valor para x
    v[j] = x;
    build_block(j / B);
  }
  else{ // caso seja uma query
    scanf("%d %d", &l, &r);
    printf("%lld\n", query(l, r));
  }
}
```

Só precisamos construir corretamente as funções `build_block` e `query`.

Na função `build_block` recebemos o indice `id` do bloco que queremos montar a resposta. Temos que tratar 2 casos, quando `id` for o último bloco ele cobrirá `[id*B, n)`, caso contrário cobrirá `[id*B, (id+1)*B)`. Podemos tratar ambos os casos assumindo que cobrirá `[id, min((id+1)*B, n))`.

```cpp
void build_block(int id){
  sum_block[id] = 0;
  int limit = min((id+1)*B, n);
  for(int i = id * B; i < limit; i++){
    sum_block[id] += v[i];
  }
}
```
A complexidade do `build_block` e consequentemente do update é `O(B)`.

Fazer a query agora pode ser um pouco complicado. Caso `l` e `r` estejam no mesmo bloco, essa divisão de blocos não nos ajuda em nada e precisamos passar por todos os elementos do intervalo, resolvendo este caso em `O(B)`. Para os outros casos sabemos que existe um intervalo de blocos que estão completamente dentro da query e podemos pegar a resposta para cada com o vetor `sum_block` em tempo `O(n / B)`. Por fim, o resto da query consiste de um sufixo do bloco do l e um prefixo do bloco do r, como no primeiro caso precisamos passar pelo bloco todo para pegar a resposta. Temos tempo de query `O(B + n / B))`.
```cpp
ll query(int l, int r){
  ll ans = 0;
  int idl = l/B, idr = r/B;
  if(idl == idr){
    for(int i = l; i <= r; i++)
      ans += v[i];
    return ans;
  }
  for(int i = l; i < (idl+1)*B; i--)
    ans += v[i];
  for(int i = idl+1; i < idr; i++)
    ans += sum_block[i];
  for(int i = idr*B; i <= r; i++)
    ans += v[i];
  return ans;
}
```

A complexidade total fica `O(q*(B + n/B))`, podemos [provar](https://www.wolframalpha.com/input/?i=minimize+x+%2B+n%2Fx,+for+x+%3E+0,+n+%3E+0) que o valor de `B` para que `B + n/B` seja mínimo é `sqrt(n)`. Portanto complexidade total `O(q*sqrt(n))`.

Apesar de no fim obtermos praticamente a mesma complexidade para o mesmo problema usando duas abordagens diferentes, divindindo as queries em blocos e dividindo o vetor em blocos alguns tipos de problemas permitem/facilitam o uso de só uma delas.

Na última versão, observe que podemos modificar facilmente as funções `build_block` e `query` para lidarmos com outros tipos de consultas sobre intervalos como menor elemento, maior elemento, gcd dos elementos etc.


## Algoritmo de Mo

Suponha que um problema dê um vetor `v` de tamanho `n`, um inteiro `q` e em seguida `q` consulta, cada consulta dá dois inteiros `l` e `r` e pergunta quantas vezes o elemento que mais aparece no intervalo aparece.

Usaremos uma ideia um pouco não convencional. Manteremos um intervalo e sua respectiva resposta. Se conseguirmos adicionar um elemento na resposta e remover um elemento da resposta podemos editar o intervalo atual para que seja igual da query.

No código o intervalo é representado por `[L, R]`, intervalo fechado nos dois extremos.

```cpp
struct mystruct{ int id, l, r; };

// .. dentro da main
vector<mystruct> queries;
for(int i = 0; i < q; i++){
  scanf("%d %d", &l, &r);
  queries.emplace_back(i, l, r);
}

cur_ans = 0; //variavel global com a resposta atual
int L = 0, R = -1; //intervalo vazio, opcionalmente
//pode-se começar com intervalo unitário
//fazendo L = 0, R = 0 e add(v[0])
for(auto query : queries){
  while(R < query.r) R++, add(v[R]);
  while(L > query.l) L--, add(v[L]);
  while(R > query.r) rem(v[R]), R--;
  while(L < query.l) rem(v[L]), L++;
  ans[query.id] = cur_ans;
}

for(int i = 0; i < q; i++)
  printf("%d\n", ans[i]);
```

Se mantermos um vetor `frequency` onde `frequency[x]` é a quantidade de vezes que o elemento `x` aparece no intervalo considerado podemos fazer a função `add` sem problemas.

```cpp
//considere que frequency está zerado antes
void add(int val){
  frequency[val]++;
  if(frequency[val] > cur_ans)
    cur_ans = frequency[val];
}
```

A função `rem` parece mais complicada pois se uma ocorrencia do elemento que aparece mais vezes for removida a resposta precisa ser decrementada somente se não há mais nenhum elemento com a mesma quantidade de ocorrencia. Usaremos então outro vetor, `howmany` onde `howmany[x]` é a quantidade de `frequency[y] = x` para todo `y` no intervalo considerado. A função `add` também precisa ser alterada com a adição do novo vetor, segue o código:

```cpp
//considere que frequency e howmany estão zerados antes
void add(int val){
  howmany[frequency[val]]--;
  frequency[val]++;
  howmany[frequency[val]]++;
  if(frequency[val] > cur_ans)
    cur_ans = frequency[val];
}

void rem(int val){
  howmany[frequency[val]]--;
  frequency[val]--;
  howmany[frequency[val]]++;
  if(!howmany[cur_ans]) cur_ans--;
}
```

Depois de tanto trabalho vamos analisar a complexidade da solução. As funções `add` e `rem` tem complexidade `O(1)`, portanto a complexidade da solução é o quanto os indices `L` e `R` precisam se mexer ao longo das consultas. É fácil perceber que se tivermos alternadamente consultas nos intervalos `[0, 0]` e `[n-1, n-1]` teremos que andar pelo vetor todo sempre, ou seja, `O(q*n)`.

Entretanto, percebemos que se os intervalos não mudassem muito entre si os indices teriam que andar pouco. Já que estamos salvando todas as consultas respondendo todas e só depois mostrando a resposta, tratar as consultas uma outra ordem pode ser melhor para nós.

```cpp
struct mystruct{ int id, l, r; };

//-------Nova parte A------
bool cmp(mystruct a, mystruct b){
  if(a.l / B != b.l / B)
    return a.l / B < b.l / B;
  return a.r < b.r;
}
//---Fim da nova parte A---

// .. dentro da main
vector<mystruct> queries;
for(int i = 0; i < q; i++){
  scanf("%d %d", &l, &r);
  queries.emplace_back(i, l, r);
}

//-------Nova parte A------
sort(queries.begin(), queries.end(), cmp);
//---Fim da nova parte A---

cur_ans = 0; //variavel global com a resposta atual
int L = 0, R = -1; //intervalo vazio, opcionalmente
//pode-se começar com intervalo unitário
//fazendo L = 0, R = 0 e add(v[0])
for(auto query : queries){
  while(R < query.r) R++, add(v[R]);
  while(L > query.l) L--, add(v[L]);
  while(R > query.r) rem(v[R]), R--;
  while(L < query.l) rem(v[L]), L++;
  ans[query.id] = cur_ans;
}

for(int i = 0; i < q; i++)
  printf("%d\n", ans[i]);
```

Agora tomando `B = sqrt(n)`, obtemos complexidade `O((q+n)*sqrt(n))`.
