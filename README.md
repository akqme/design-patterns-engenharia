# Implementação de Padrões de Projeto
Trabalho realizado para a disciplina de Engenharia de Software com implementação de 3 padrões de projeto clássicos, abrangendo as categorias comportamental, criacional e estrutural.

## Padrões implementados
| Padrão        | Categoria      | Arquivo       |
|---------------|----------------|---------------|
| Iterator      | Comportamental | iterator.cpp  |
| Builder       | Criacional     | builder.cpp   |
| Composite     | Estrutural     | composite.cpp |
## Tecnologias Utilizadas
* Linguagem: C++
* Ferramentas: Compilador C++ (g++), editor de texto/código
* Referência: [Refactoring Guru](https://refactoring.guru/design-patterns/)

## Padrão de Projeto Comportamental - Iterator
Em muitos programas, existe a necessidade de percorrer uma coleção de elementos (listas, árvores, vetores, tabelas etc.).
O problema é que:
* Cada coleção pode ter estrutura interna diferente;
* O cliente não deveria depender de como os elementos são armazenados;
* Coleções diferentes obrigariam o cliente a criar vários loops diferentes.

Isso gera acoplamento entre o cliente e a estrutura interna da coleção, ou seja, o cliente precisa percorrer a coleção, mas não deveria saber como ela funciona por dentro.
O padrão Iterator separa:
* A coleção, que guarda os elementos;
* O iterador, que sabe percorrer esses elementos.

A interface iterador percorre os elementos com funções como: `primeiro()` e `próximo()`, enquanto a coleção cria seus próprios iteradores, garantindo que o cliente não precise saber como os elementos são armazenados e utilize os mesmos métodos para percorrer uma coleção, independente do tipo. Isso permite percorrer listas, vetores, árvores, conjuntos etc. usando a mesma lógica.

## Explicação do código - iterator.cpp
O código implementado trata-se de uma coleção de nomes, que será percorrido usando o padrão iterator, ou seja, ele demonstra como percorrer uma coleção de elementos sem expor sua estrutura interna.
### Interface Iterator
```cpp
class Iterator {
public:
    virtual ~Iterator() {}
    virtual void primeiro() = 0;
    virtual void prox() = 0;
    virtual bool fim() const = 0;
    virtual string atual() const = 0;
};
```
Define como o iterador deve se comportar, com os métodos de controle.
### Classe NameIterator
```cpp
class NomeCollection;

class NomeIterator : public Iterator {
private:
    const NomeCollection& collection;
    size_t index;

public:
    NomeIterator(const NomeCollection& col);
    
    void primeiro() override;
    void prox() override;
    bool fim() const override;
    string atual() const override;
};
```
Implementa o mecanismo de navegação sobre a coleção sem expor seu vetor interno.
### Uso do Padrão
```cpp
int main() {
    NomeCollection Nomes;
    Nomes.adicionar("Igor");
    Nomes.adicionar("Henrique");
    Nomes.adicionar("Gustavin");
    
    Iterator* it = Nomes.createIterator();

    for (it->primeiro(); !it->fim(); it->prox()) {
        cout << it->atual() << endl;
    }

    delete it;
}
```
Percorre a coleção usando somente os métodos do Iterator.

## Diagrama UML - Iterator

![Diagrama UML - Iterator](https://uml.planttext.com/plantuml/png/jL9BJiD03DtFARoaGka1eYegeXka8jXm07VYH8EI6SdZe4g5qt0KBeQJJ9EMi613TcE_v_THjb6GfMiR8quQWcSi-Uipm8CGemI637OEQyOMuULnRuppkgdGqWmv6T3l5Yh7KMvMaCDRS6KQUZekPYxkWgTXUWYXILFRCPCNFTiEgwV3Aran7vUQTtNDLAFG96gPz7dsCX3FZvvg6umHxeDNt0nBx0nmkfTFe4aZOP8lHQYF_uvwxVTdt0oSKg8ozYxAPgGLVsXYHDGqIZfVqd7CSw6UheJEZodwjEfewcpplbulcZIpnQlzNEAY-omhVWcO0rD5sefr28KnM_9b_rj-0m00)

## Padrão de Projeto Criacional - Builder
Em muitos sistemas, o processo de construir um objeto complexo (com muitas etapas ou partes opcionais) torna-se complicado e difícil de manter. Além disso, diferentes versões/variações do mesmo objeto podem precisar ser construídas de formas diferentes, mas compartilhando uma mesma sequência de passos. O **Builder** separa o processo de construção do produto final, permitindo criar objetos complexos de forma clara e reutilizável. Nesse padrão, é possível criar vários **"builders"** que criaram diferentes versões do mesmo produto. Este padrão tem como benefíco tornar o código de construção fica isolado e organizado.

## Explicação do código - builder.cpp
Esse código trata-se de um montador de sanduíches.

### Definição do Produto
```cpp
class Sanduiche {
public:
    string pao;
    string carne;
    string molho;
    string salada;

    void mostrar() const {
        cout << "\n--- Lanche Pronto ---\n";
        cout << "Pão: " << pao << "\n";
        cout << "Carne: " << carne << "\n";
        cout << "Molho: " << (molho.empty() ? "Nenhum" : molho) << "\n";
        cout << "Salada: " << (salada.empty() ? "Nenhuma" : salada) << "\n";
    }
};
```
Representa o objeto sendo construído e possui um método para exibição.

### Builder Genérico
```cpp
class SanduicheBuilder {
public:
    virtual ~SanduicheBuilder() {}

    virtual void setPao() = 0;
    virtual void setCarne() = 0;
    virtual void setMolho() = 0;
    virtual void setSalada() = 0;

    virtual Sanduiche getSanduiche() = 0;
};
```
Define o conjunto de etapas necessárias para montar qualquer sanduíche.

### Builder Concreto

```cpp
class HamburguerBuilder : public SanduicheBuilder {
private:
    Sanduiche lanche;

public:
    void setPao() override { lanche.pao = "Pão de hambúrguer"; }
    void setCarne() override { lanche.carne = "Carne bovina"; }
    void setMolho() override { lanche.molho = "Maionese"; }
    void setSalada() override { lanche.salada = "Alface e tomate"; }

    Sanduiche getSanduiche() override { return lanche; }
};
```
Implementa cada etapa para um hambúrguer. (O builder de cachorro quente funciona da mesma forma).

Um sanduíche possui pão, carne, molho e salada, como definido na classe Sanduiche. `SanduicheBuilder` é o builder genérico que mostra como deve ser contruído um lanche. Neste código, temos dois builders concretos: `HamburguerBuilder` e `CachorroQuenteBuilder`, que têm a função de contruir os lanches de acordo com seus respectivos nomes. Por fim temos o diretor, que sabe como construir um lanche comum, sem salada ou sem molho. Um builder é passado a ele, que cria o lanche da forma pedida, independente do tipo (Hamburguer ou Cachorro Quente).

### Diretor
```cpp
class Diretor {
private:
    SanduicheBuilder& builder;

public:
    Diretor(SanduicheBuilder& b) : builder(b) {}

    Sanduiche montarNormal() {
        builder.setPao();
        builder.setCarne();
        builder.setSalada();
        builder.setMolho();
        return builder.getSanduiche();
    }

    Sanduiche montarSemMolho() {
        builder.setPao();
        builder.setCarne();
        builder.setSalada();
        return builder.getSanduiche();
    }
};
```
Define diferentes variações de montagem reutilizando o mesmo Builder.

### Uso do Padrão
```cpp
int main() {
    HamburguerBuilder hb;
    Diretor diretor1(hb);

    Sanduiche hamburguer = diretor1.montarNormal();
    hamburguer.mostrar();

    CachorroQuenteBuilder cb;
    Diretor diretor2(cb);

    Sanduiche dogSemSalada = diretor2.montarSemSalada();
    dogSemSalada.mostrar();
}
```
Demonstra como montar diferentes lanches com diferentes builders.

## Diagrama UML - Builder
![Diagrama UML - Builder](https://uml.planttext.com/plantuml/png/pP91JiCm44NtFiKiYv1xWAYAIbjrrKgH40Tmx24QoR4ZUy86E0r7uM8ur4aWfNkyikQD_lm_ccra5RXlhM1YYr0f4pu_FEnwiWO3I3YGQe9g5JoTJq9egsA4AdZJQuPN0UdTGvSq8XHmeiYRHmxacZ8ZcqhLW5TtgU75an7lGf1Z3CzAupHeb0eOaUVUIx4UcWQLXvjaTvFibwH1pW6-IVuFjYxfzjxfW8oZDMexYosw5F6tMmbTNiIiyczI72YWPZzxhRFtOaoHXPBJN5ZLC_pBnvaSjSh-7BjiEVRscYTFuyLuAPVx5UDIDs_hzHMSOaXPJkile8yAIY6swCnml5y0)

## Padrão de Projeto Estrutural - Composite
Às vezes você precisa representar uma estrutura hierárquica, como um sistemas de arquivos. O problema é que você terá objetos que são simples (folhas) e outros que contêm outros objetos (compostos) e muitas vezes você precisar tratar eles de forma igual. Sem **Composite**, seria necessário tratar cada tipo separadamente, criando muitos `if/else` e lógica duplicada. Para resolver isso, o **Composite** propõe:
* Criar uma interface comum para folhas e contêineres;
* Fazer com que objetos compostos armazenem outros objetos que implementam a mesma interface;
* Permitir que o cliente trate uma “parte” e um “todo” da mesma forma.

Com isso, o composite permite que objetos simples e complexos sejam tratados de forma igual e que esses objetos sejam adicionados sem a 

## Explicação do código - composite.cpp
O código implementado trata-se de um sistema de arquivos simples, com pastas e arquivos.
### Interface base para arquivo/pasta
```cpp
class ItemSistema {
public:
    virtual ~ItemSistema() {}
    virtual void mostrar(int nivel = 0) const = 0;
    virtual int tamanho() const = 0;
};
```
Garante que arquivos e pastas tenham comportamento comum.

### Objeto folha: Arquivo
```cpp
class Arquivo : public ItemSistema {
private:
    string nome;
    int tam;

public:
    Arquivo(string n, int t) : nome(n), tam(t) {}

    void mostrar(int nivel = 0) const override {
        cout << string(nivel, '-') << nome << " (" << tam << " KB)\n";
    }

    int tamanho() const override {
        return tam;
    }
};
```
Representa um item simples (sem filhos).

### Objeto composto: Pasta
```cpp
class Pasta : public ItemSistema {
private:
    string nome;
    vector<unique_ptr<ItemSistema>> itens;

public:
    Pasta(string n) : nome(n) {}

    void adicionar(unique_ptr<ItemSistema> item) {
        itens.push_back(move(item));
    }

    void mostrar(int nivel = 0) const override {
        cout << string(nivel, '-') << "[" << nome << "]\n";
        for (auto &item : itens) {
            item->mostrar(nivel + 2);
        }
    }

    int tamanho() const override {
        int soma = 0;
        for (auto &item : itens) soma += item->tamanho();
        return soma;
    }
};
```
Representa uma pasta contendo outros arquivos ou pastas, com comportamento recursivo.
### Uso do padrão

```cpp
int main() {
    auto raiz = make_unique<Pasta>("Trabalho-Engenharia");

    auto strategy = make_unique<Pasta>("padraoComportamental - strategy");
    auto builder = make_unique<Pasta>("padraoCriacional - builder");
    auto composite = make_unique<Pasta>("padraoEstrutural - composite");

    strategy->adicionar(make_unique<Arquivo>("strategy.cpp", 120));
    builder->adicionar(make_unique<Arquivo>("builder.cpp", 300));
    composite->adicionar(make_unique<Arquivo>("composite.cpp", 450));

    raiz->adicionar(move(strategy));
    raiz->adicionar(move(builder));
    raiz->adicionar(move(composite));
    raiz->adicionar(make_unique<Arquivo>("README.md", 10));

    raiz->mostrar();
    cout << "\nTamanho total da pasta Trabalho-Engenharia: " << raiz->tamanho() << " KB\n";
}
```
Cria uma estrutura de pastas e arquivos e calcula seu tamanho total utilizando o Composite.

O código implementado trata-se de um sistema de arquivos simples, com pastas e arquivos. A interface ItemSistema definine as funções de mostrar e ver o tamanho, que será utilizada tanto para as pastas, quanto para os arquivos. No objeto do tipo pasta é possível adicionar mais itens, inclusive mais pastas, formando assim uma estrutura hierárquica recursiva, ideal para o padrão **Composite**. Com esse código, é possível criar pastas e arquivos, além de poder mostrar seus conteúdos e tamanho de forma clara ao cliente.

## Diagrama UML - Composite
![Diagrama UML - Composite](https://uml.planttext.com/plantuml/png/ZP512W8n34NtFKMM8z8BY8YWon7SU83GPZGmRQMDWw2UnfNdc8ivMgdYGimo_v6V_CoJO9I3QvMmj0HhjB6_1bW4jm-9XK33ad4RqI5iLhLYBnGRD9GHJrxWf62eCOGzHHGElXh11Bh0LbsKCYsc13LXynVuTiwq1hFZraRo0rfpacandht6eRML6TGYbAbPZ-H2Hp-HBSdYOLEnjtGSDCv8uNzk_evYUjRwUUjthvpredEsUlOHumHCyDBVd59pylRnb3i0)

# Conclusão

Este trabalho demonstrou com sucesso a implementação prática de três padrões fundamentais de projeto: Iterator (comportamental), Builder (criacional) e Composite (estrutural). Através das implementações em C++, foi possível observar como cada padrão aborda problemas específicos de design de software de maneira elegante e eficiente.

Os padrões aplicados mostraram-se essenciais para:

* Reduzir acoplamento entre componentes do sistema
* Aumentar a flexibilidade do código para futuras extensões
* Melhorar a organização e legibilidade do código
* Promover a reutilização de componentes

A experiência comprovou que o conhecimento e aplicação correta de padrões de projeto são ferramentas valiosas no arsenal de qualquer engenheiro de software, permitindo a criação de sistemas mais robustos, maintaináveis e escaláveis. Cada padrão resolveu um problema distinto, mas todos contribuíram para um design de software mais limpo e profissional.

Este trabalho serve como base sólida para a compreensão e aplicação de outros padrões em projetos futuros, reforçando a importância de boas práticas de engenharia de software no desenvolvimento de sistemas complexos.