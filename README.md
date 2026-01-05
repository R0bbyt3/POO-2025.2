
> Esse repositório é um arquivamento da matéria de **Programação Orientada a Objeto**, cursada em **2025.2**.
>
> **Estrutura do repositório (arquivos principais):**
> - `controller/`
> - `model/`
> - `view/`
>
> No mesmo diretório, a pasta/arquivo `zip/` contém o **projeto completo**, que pode ser aberto no **Eclipse** usando **Java/JDK 24**.

# MONOPOLY GAME

Integrantes: **Robbie Carvalho**  ; **Theo Canuto**

---

## 1. Informações gerais

- Extensão: `.txt`
- Separador: vírgula (`,`)
- Linhas de comentário: começam com `#`
- Seções: delimitadas por linhas de cabeçalho iniciadas com `#`

---

## 2. Estrutura do arquivo

O arquivo é dividido nas seções abaixo, na seguinte ordem:

1. `GAME_INFO`: informações gerais da partida  
2. `PLAYERS`: dados de todos os jogadores  
3. `PROPERTIES_STREETS`: propriedades do tipo rua (com construções)  
4. `PROPERTIES_COMPANIES`: propriedades do tipo companhia  
5. `DECK_STATE`: estado do baralho de cartas  
6. `JAIL_CARDS_OUT`: cartas “Sair da Prisão” fora do baralho  

---

## 3. Seção: `GAME_INFO`

Contém informações gerais sobre o estado da partida.

**Formato:**
```txt
# GAME_INFO
currentPlayerIndex,<indice>
numberOfPlayers,<quantidade>
bankCash,<dinheiro_do_banco>
````

**Campos:**

* `currentPlayerIndex`: índice do jogador cuja vez está ativa

  * valores válidos: `0` até `(numberOfPlayers - 1)`
* `numberOfPlayers`: quantidade total de jogadores na partida

  * valores válidos: `3` a `6`
* `bankCash`: quantidade atual de dinheiro no banco

  * valores válidos: inteiro `>= 0`
  * valor inicial padrão: `200000`

**Exemplo:**

```txt
# GAME_INFO
currentPlayerIndex,2
numberOfPlayers,4
bankCash,195000
```

---

## 4. Seção: `PLAYERS`

Contém os dados de todos os jogadores da partida.

**Formato:**

```txt
# PLAYERS
# id,name,color,money,position,inJail,getOutOfJailCards,alive
<id_jogador1>,<nome1>,<cor1>,<dinheiro1>,<posicao1>,<prisao1>,<cartas1>,<vivo1>
<id_jogador2>,<nome2>,<cor2>,<dinheiro2>,<posicao2>,<prisao2>,<cartas2>,<vivo2>
...
```

**Campos:**

* `id`: identificador único do jogador (ex: `"player1"`, `"player2"`)

  * formato: string sem espaços ou vírgulas
* `name`: nome do jogador exibido no jogo

  * formato: string (evitar vírgulas)
  * padrão: `"Player 1"`, `"Player 2"`, etc.
* `color`: cor do peão do jogador

  * valores válidos: `RED`, `BLUE`, `GREEN`, `YELLOW`, `ORANGE`, `PURPLE`
  * deve corresponder aos valores do enum `PlayerColor`
* `money`: quantidade de dinheiro que o jogador possui

  * valores válidos: inteiro `>= 0`
  * dinheiro inicial padrão: `4000`
* `position`: posição atual do jogador no tabuleiro (índice da casa)

  * valores válidos: `0` a `39` (tabuleiro tem 40 casas)
  * posição inicial: `0` (casa `"Start"`)
* `inJail`: indica se o jogador está preso

  * valores válidos: `true` ou `false`
* `getOutOfJailCards`: quantidade de cartas “Sair da Prisão” que o jogador possui

  * valores válidos: inteiro `>= 0` (no contexto do jogo, `0` ou `1`)
* `alive`: indica se o jogador ainda está no jogo (não faliu)

  * valores válidos: `true` ou `false`
  * se `false`, o jogador está eliminado e seu peão não aparece no tabuleiro

**Exemplo:**

```txt
# PLAYERS
# id,name,color,money,position,inJail,getOutOfJailCards,alive
player1,Player 1,RED,1500,10,false,0,true
player2,Player 2,BLUE,0,15,false,0,false
player3,Player 3,GREEN,2000,5,false,1,true
player4,Player 4,YELLOW,800,22,true,0,true
```

**Observações:**

* `player2` está falido (`alive=false`, `money=0`)
* `player3` possui uma carta “Sair da Prisão”
* `player4` está preso (`inJail=true`)

---

## 5. Seção: `PROPERTIES_STREETS`

Contém informações sobre propriedades do tipo **RUA** que possuem dono.
Inclui dados sobre construções (casas e hotéis).

**Formato:**

```txt
# PROPERTIES_STREETS
# squareIndex,ownerId,houses,hasHotel
<indice1>,<dono1>,<casas1>,<hotel1>
<indice2>,<dono2>,<casas2>,<hotel2>
...
```

**Campos:**

* `squareIndex`: índice da casa no tabuleiro

  * valores válidos: `0` a `39`
  * apenas casas do tipo `"StreetOwnableSquare"`
* `ownerId`: ID do jogador que possui a propriedade

  * deve corresponder ao campo `id` em `PLAYERS` (ex: `"player1"`)
* `houses`: quantidade de casas construídas na propriedade

  * valores válidos: `0` a `4`
  * `0` = sem casas (apenas terreno)
* `hasHotel`: indica se há um hotel construído

  * valores válidos: `true` ou `false`
  * regra: se `hasHotel=true`, então `houses` **deve** ser `> 0`

**Exemplo:**

```txt
# PROPERTIES_STREETS
# squareIndex,ownerId,houses,hasHotel
1,player1,2,false
3,player1,2,true
6,player3,0,false
8,player3,4,true
11,player1,1,false
```

**Observações:**

* Propriedades sem dono **não** aparecem nesta seção
* Casa índice `8` tem hotel (`houses=4`, `hasHotel=true`)
* Casa índice `6` é apenas terreno (`houses=0`, `hasHotel=false`)

---

## 6. Seção: `PROPERTIES_COMPANIES`

Contém informações sobre propriedades do tipo **COMPANHIA** que possuem dono.
Companhias não têm construções, apenas propriedade.

**Formato:**

```txt
# PROPERTIES_COMPANIES
# squareIndex,ownerId
<indice1>,<dono1>
<indice2>,<dono2>
...
```

**Campos:**

* `squareIndex`: índice da casa no tabuleiro

  * valores válidos: `0` a `39`
  * apenas casas do tipo `"CompanyOwnableSquare"`
* `ownerId`: ID do jogador que possui a propriedade

  * deve corresponder ao campo `id` de um jogador em `PLAYERS`

**Exemplo:**

```txt
# PROPERTIES_COMPANIES
# squareIndex,ownerId
12,player2
28,player2
5,player4
```

**Observações:**

* Propriedades sem dono **não** aparecem nesta seção
* Companhias não têm casas ou hotéis

---

## 7. Seção: `DECK_STATE`

Contém informações sobre o estado do baralho de cartas, preservando a ordem exata das cartas restantes no deck.
Essa ordem é crítica pois cada carta tirada vai para o final da fila, e ao carregar uma partida salva, o jogo deve continuar exatamente de onde parou.

**Formato:**

```txt
# DECK_STATE
# Ordem completa das cartas no baralho (do topo ao fim)
# cardId,cardType,cardValue
<id_carta1>,<tipo1>,<valor1>
<id_carta2>,<tipo2>,<valor2>
...
```

**Campos:**

* `cardId`: identificador único da carta no baralho

  * valores válidos: inteiro `>= 0`
  * corresponde ao índice original da carta no arquivo de configuração
* `cardType`: tipo da carta (define o efeito quando tirada)

  * valores válidos:

    * `PAY_BANK`: jogador paga ao banco
    * `RECEIVE_BANK`: jogador recebe do banco
    * `PAY_ALL`: jogador paga a todos os outros jogadores vivos
    * `RECEIVE_ALL`: jogador recebe de todos os outros jogadores vivos
    * `GO_TO_JAIL`: jogador vai para a prisão
    * `GET_OUT_OF_JAIL`: jogador ganha carta de saída livre da prisão
* `cardValue`: valor monetário associado à carta (se aplicável)

  * valores válidos: inteiro `>= 0`
  * usado em `PAY_BANK`, `RECEIVE_BANK`, `PAY_ALL`, `RECEIVE_ALL`
  * para `GO_TO_JAIL` e `GET_OUT_OF_JAIL`, este valor é sempre `0`

**Exemplo:**

```txt
# DECK_STATE
# Ordem completa das cartas no baralho (do topo ao fim)
# cardId,cardType,cardValue
5,PAY_BANK,50
12,RECEIVE_BANK,100
3,GO_TO_JAIL,0
8,PAY_ALL,25
1,RECEIVE_BANK,200
15,PAY_BANK,75
```

**Observações:**

* A ordem das linhas representa a ordem das cartas no deck (primeira linha = topo)
* Cartas `GET_OUT_OF_JAIL` que foram tiradas e estão com jogadores **não** aparecem nesta lista (foram removidas do deck)
* Ao carregar, o deck é reconstruído exatamente nessa ordem

---

## 8. Seção: `JAIL_CARDS_OUT`

Contém informações sobre cartas “Sair da Prisão” que foram retiradas do baralho e estão em posse dos jogadores.

**Formato:**

```txt
# JAIL_CARDS_OUT
getOutOfJailCardsOut,<quantidade>
```

**Campos:**

* `getOutOfJailCardsOut`: quantidade de cartas “Sair da Prisão” que foram retiradas do baralho e estão com jogadores

  * valores válidos: inteiro `>= 0`
  * deve ser igual à soma de `getOutOfJailCards` de todos os jogadores

**Exemplo:**

```txt
# JAIL_CARDS_OUT
getOutOfJailCardsOut,1
```

**Observações:**

* Se nenhum jogador tiver cartas, o valor será `0`
* A soma dos `getOutOfJailCards` de todos os jogadores deve ser igual a este valor

---

## 9. Exemplo completo de arquivo

```txt
# GAME_INFO
currentPlayerIndex,0
numberOfPlayers,3
bankCash,198500

# PLAYERS
# id,name,color,money,position,inJail,getOutOfJailCards,alive
player1,Player 1,RED,1850,15,false,1,true
player2,Player 2,BLUE,500,8,true,0,true
player3,Player 3,GREEN,0,20,false,0,false

# PROPERTIES_STREETS
# squareIndex,ownerId,houses,hasHotel
1,player1,2,false
3,player1,2,false
6,player1,0,false
11,player2,1,false
13,player2,1,false

# PROPERTIES_COMPANIES
# squareIndex,ownerId
12,player1
28,player2

# DECK_STATE
# Ordem completa das cartas no baralho (do topo ao fim)
# cardId,cardType,cardValue
7,RECEIVE_BANK,150
2,PAY_BANK,100
14,PAY_ALL,50
9,RECEIVE_ALL,20
4,GO_TO_JAIL,0
11,RECEIVE_BANK,75
6,PAY_BANK,25

# JAIL_CARDS_OUT
getOutOfJailCardsOut,1
```

```

Reformatei a partir do teu `leiame.txt`. :contentReference[oaicite:0]{index=0}  
A versão “mais recente e estável” em **outubro de 2025** é **JDK 25.0.1 (21/out/2025)**. :contentReference[oaicite:1]{index=1}
::contentReference[oaicite:2]{index=2}
```
