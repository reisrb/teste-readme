# SITE-INSTITUCIONAL-PI

Exemplos de como alterar e executar as [API](https://www.redhat.com/pt-br/topics/api/what-are-application-programming-interfaces).

* [Configurando banco de dados da API SENSOR](#api-sensor-dht11)
* [Configurando banco de dados da API SITE](#api-site)
* [Criando uma nova rota na API](#criar-nova-rota-na-api)
  * [1. Importando arquivo que contém as rotas](#importando-arquivo-que-contém-as-rotas)
  * [2. Criando uma porta de entrada na API](#criando-uma-porta-de-entrada-na-api)
  * [3. Criando arquivo de rota](#criando-arquivo-de-rota)
  * [4. Criando arquivo controller (regra de negócio)](#criando-arquivo-controller)
  * [5. Criando arquivo models](#criando-arquivo-models)


## API-SENSOR-DHT11

Para estabelecer a conexão com o banco, siga esse caminho:

![Screenshot from 2021-11-13 03-12-26](https://user-images.githubusercontent.com/89267557/141608292-4699e891-86b2-450b-b1fc-504c38ae9cc0.png)

Altere o seguinte bloco de código:

![Screenshot from 2021-11-13 03-20-32](https://user-images.githubusercontent.com/89267557/141608446-74491ee4-0079-4544-8a92-1bed7c653665.png)

Para estabelecer uma conexão deve ser alterado o **user:"XXXXX"** para **user:"root"** e **password:"XXXXX"** para **password:"senhaDoSeuBanco"** por exemplo (**a senha muda de configuração para configuração do banco na máquina**).

```bash
user:"XXXXX" => user:"root"
password:"XXXXX" => password:"123456"
```

Dentro do diretório api-sensor-dht11 execute os seguintes comandos:

**Instale todos os pacotes pendentes:**

```bash
npm install
```

**Inicie a aplicação para gerar dados aleatórios:**

```bash
npm start
```

### Inserção dos dados no banco

Para inserir os dados gerados ou captados do arduino, abra o arquivo **index.html** dentro de **api-sensor-dht11** e veja os gráficos se movendo, se estiver tudo ok, pode ir no terminal de comando e ver as medidas sendo inseridas na tabela de **medida** do database **acquatec** no banco!



## API-SITE


Para estabelecer a conexão com o banco, siga esse caminho:

![Screenshot from 2021-11-13 03-40-16](https://user-images.githubusercontent.com/89267557/141608942-edccf394-b87a-42c2-b500-285722175c18.png)

Altere o seguinte bloco de código:

![Screenshot from 2021-11-13 03-42-09](https://user-images.githubusercontent.com/89267557/141609023-e93d7cfc-d54a-4013-980e-6c995afd9057.png)

Para estabelecer uma conexão deve ser alterado o **user:"XXXXX"** para **user:"root"** e **password:"XXXXX"** para **password:"senhaDoSeuBanco"** por exemplo (**a senha muda de configuração para configuração do banco na máquina**).

```bash
user:"XXXXX" => user:"root"
password:"XXXXX" => password:"123456"
```

**Para o banco de dados funcionar é preciso estar configurado! Como visto no diagrama acima, temos o diretório database e dentro de database possui um arquivo chamado sql.sql que contém um código (um comentário da metade pra baixo) de criação do banco mysql local!**

Dentro do diretório api-site execute os seguintes comandos:

**Instale todos os pacotes pendentes:**

```bash
npm install
```

**Inicie a aplicação:**

```bash
npm start
```

Acesse [localhost:3333](http://localhost:3333) para usar o front!

## CRIAR NOVA ROTA NA API

Para criar uma nova rota na API, siga esse caminho:

### Para esse exemplo, irei criar um cadastro e consulta de carros!

Com a tabela criada no banco, tente usar uma rota já existente se fizer sentido. Se não fizer sentido, siga os próximos passos.

### Importando arquivo que contém as rotas
Vá na app.js que fica em api-site e adicione uma linha adicionando o arquivo de rotas que você irá usar! No meu caso, na linha 15 adicionei **carrosRouter** importando o arquivo **carros.js** da pasta **routes**:

```node
var app = express();

var indexRouter = require("./src/routes/index");
var usuarioRouter = require("./src/routes/usuarios");
var avisosRouter = require("./src/routes/avisos");
var medidasRouter = require("./src/routes/medidas");
var carrosRouter = require("./src/routes/carros");
```


### Criando uma porta de entrada na api
Ainda na app.js, com a importação criada, é possível criarmos uma "rota"! Como seria? Irei descer mais um pouco o arquivo e iremos nos deparar com o seguinte código e irei adicionar uma "rota" nova para a minha API:

```node
app.use(cors());

app.use("/", indexRouter);
app.use("/usuarios", usuarioRouter);
app.use("/avisos", avisosRouter);
app.use("/medidas", medidasRouter);
app.use("/carros", carrosRouter);
```
**Pode perceber que logo depois de /medidas eu adicionei um /carros e chamei a carrosRouter importada anteriormente!**

### Criando arquivo de rota
Logo após ter feito a primeira "porta de entrada" da nossa API, quando um front-end for acessar essa nossa rota, ele deve colocar /carros/**algumaCoisa**, o que seria esse "alguma coisa"? Dentro de api-site -> src -> routes, irei criar um novo arquivo chamado carros.js que é nesse arquivo que eu importo no [passo 1. Importando arquivo que contém as rotas](#importando-arquivo-que-contém-as-rotas)

No arquivo carros.js dentro de routes, devemos criar exatamente nessa estrutura:

![image](https://user-images.githubusercontent.com/89267557/142450112-2ca141e1-7950-4655-9737-f4c1f7df1a3d.png)

**Configuração padrão para indicar o uso da biblioteca do node para criação:**
```node
var express = require("express");
var router = express.Router();
```

**Importando a controller que vai ser criada posteriormente:**
```node
var carroController = require("../controllers/carroController");
```

**Criando a rota que vai indicar /carros/cadastrar ao ser acessada pelo front-end. O /cadastrar é do tipo post e o /listar é do tipo get:**
```node
router.post("/cadastrar", function (req, res) {
    // função a ser chamada quando acessar /carros/cadastrar
    carroController.cadastrar(req, res);
});

router.get("/listar", function (req, res) {
    // função a ser chamada quando acessar /carros/listar
    carroController.listar(req, res);
});
```

**Sempre devemos deixar visível (exportar) para outro que assim outro arquivo consiga importar todas as nossas configurações desse arquivo:**
```node
module.exports = router;
```

Ao final de tudo, teremos o arquivo com essa estrutura:

```node
var express = require("express");
var router = express.Router();

var carroController = require("../controllers/carroController");

router.post("/cadastrar", function (req, res) {
    // função a ser chamada quando acessar /carros/cadastrar
    carroController.cadastrar(req, res);
});

router.get("/listar", function (req, res) {
    // função a ser chamada quando acessar /carros/listar
    carroController.listar(req, res);
});

module.exports = router;
```

### Criando arquivo controller
Dentro de api-site -> src -> controller, crie um arquivo chamado carroController.js:

![image](https://user-images.githubusercontent.com/89267557/142464030-e1f40a62-dc9a-4cf9-8d7b-439be5f67784.png)

Dentro do arquivo chamamos a **carroModel** que fará a conexão com o banco:
```node
var carroModel = require("../models/carroModel");
```

Cria a função que vai ser chamada no passo de cima e exporta ela. No final ficará assim:
```node
var carroModel = require("../models/carroModel");

function listar(req, res) {
    carroModel.listar().then(function(resultado){
        // precisamos informar que o resultado voltará para o front-end como uma resposta em json
        res.status(200).json(resultado);
    }).catch(function(erro){
        res.status(500).json(erro.sqlMessage);
    })
}

function cadastrar(req, res) {
    var nome = req.body.nome;

    if (nome == undefined) {
        res.status(400).send("Seu nome está undefined!");
    }

    carroModel.cadastrar(nome).then(function(resposta){
        res.status(200).send("Carro criado com sucesso");
    }).catch(function(erro){
        res.status(500).json(erro.sqlMessage);
    })
}

module.exports = {
    listar, 
    cadastrar
}
```

**Sempre exportando a função que eu criar para poder ser "enxergada" por outro arquivo.**

### Criando arquivo models

Dentro de api-site -> src -> models, crie um arquivo chamado carroModel.js:

![image](https://user-images.githubusercontent.com/89267557/142464770-586e07dd-a309-4755-b4ee-067ac3855c9f.png)

Dentro do arquivo importamos a configuração do banco para fazer consultas futuras no banco:

```node
var database = require("../database/config")
```

Aqui podemos criar a função que fará a comunicação com o banco de dados, onde ela recebe o paramêtro nome e executa uma query de insert com o valor desse nome:

```node
function listar() {
    var instrucao = `
        SELECT * FROM carro;
    `;
    console.log("Executando a instrução SQL: \n" + instrucao);
    return database.executar(instrucao);
}

function cadastrar(nome) {
    var instrucao = `
        INSERT INTO carro (nome) VALUES ('${nome}'');
    `;
    console.log("Executando a instrução SQL: \n" + instrucao);
    return database.executar(instrucao);
}
```

**Sem esquecer de exportar as funções que criamos para serem vistas por outros arquivos** 
```node
module.exports = {
    cadastrar,
    listar
};
```

E por fim teremos o arquivo completo com isso:
```node
var database = require("../database/config")

function listar() {
    var instrucao = `
        SELECT * FROM carro;
    `;
    console.log("Executando a instrução SQL: \n" + instrucao);
    return database.executar(instrucao);
}

function cadastrar(nome) {
    var instrucao = `
        INSERT INTO carro (nome) VALUES ('${nome}');
    `;
    console.log("Executando a instrução SQL: \n" + instrucao);
    return database.executar(instrucao);
}

module.exports = {
    cadastrar,
    listar
};
```

