
# **Registro de Estudo: NodeJS**


## 📝 **Tópicos Abordados**
-  [NodeJS](#node) 
-  [Rotas](#rotas) 
-  [Insomnia](#inso)
-  [API](#api)
 
---

## **1. NodeJS <a id = "node"></a>**

**Node.js** é uma plataforma que permite executar JavaScript no servidor, tornando-o ideal para criar aplicações rápidas e escaláveis, como servidores e aplicativos em tempo real. Ele é eficiente em lidar com múltiplas tarefas ao mesmo tempo, usando um modelo assíncrono.


### **1.2 Rotas <a id = "rotas"></a>**

Rotas em Node.js, geralmente associadas a frameworks como o `Express`, são usadas para definir caminhos e controlar como o servidor responde a diferentes requisições **HTTP (GET, POST, PUT, DELETE, etc.)**. Elas conectam as URLs às funções do servidor que devem ser executadas quando essas URLs são acessadas.

```javascript
const express = require('express')
const app = express()
const PORT = 3000

//GET:
app.get('/', (req, res) => {
    console.log("ENTROU AQUI")
    res.send("app GET")
})

//POST:
app.post('/', (req, res) => {
    res.send("app POST")
})

//DELETE:
app.delete('/', (req, res) => {
    res.send("app DELETE")
});

app.listen(PORT, () => {
    console.log(`App online na porta ${PORT}`)
})
```



### **1.3 Insomnia <a id = "inso"></a>**
ferramenta de desenvolvimento para testar e interagir com APIs RESTful e GraphQL. Ele permite enviar requisições HTTP de maneira fácil e rápida, facilitando o processo de depuração e teste de endpoints. O Insomnia oferece uma interface gráfica simples, onde você pode configurar diferentes tipos de requisições, adicionar parâmetros, cabeçalhos, autenticação e visualizar as respostas de forma organizada.

![alt text](images/image1.png)


### **1.4 API <a id = "api"></a>**
Uma API em Node.js é uma interface de comunicação que permite que sistemas diferentes interajam com o seu servidor. No contexto do Node.js, as APIs geralmente são desenvolvidas utilizando o framework Express, que facilita o gerenciamento de rotas, requisições HTTP e resposta de dados.

No meu caso, desenvolvi uma **API de gerenciamento de lista de usuários**, como no seu caso, a API permite criar, ler, atualizar e excluir usuários em um sistema. Essas operações são conhecidas como **CRUD** (Create, Read, Update, Delete). A API recebe requisições do cliente (como um front-end ou outra aplicação) e executa a lógica para interagir com o banco de dados ou com o armazenamento de dados para realizar essas operações.

![alt text](images/image.png)


### **Implementação:** 
Nesta API, utilizamos Mongoose para facilitar a interação com o **MongoDB**, permitindo que os dados de usuários sejam salvos, recuperados, atualizados ou deletados de maneira simples e eficaz. Abaixo, explicamos as principais rotas definidas no arquivo `index.js`:

``` javascript
/*
- GET: Recuperar dados.
- POST: Criar novos dados.
- DELETE: Remover dados.
- PUT: Atualizar dados existentes.
*/

var express = require("express")
const mongoose = require('mongoose');

const app =  express()
app.use(express.json())
const port = 3000

// Importando o modelo do usuário
const User = require('./Models/User');


// GET:
// --> Vai retornar todos usuários
app.get('/', async (req, res) => {
    const users = await User.find()
    return res.send(users) 
  })

  
// GET{id}:
// --> Vai retornar o usuário pelo 'id' especificado
app.get('/:id', async (req, res) => {
  const user = await User.findById(req.params.id)
  return res.send(user) 
})


// POST:
// --> Vai enviar os dados para o BD
app.post('/', async (req, res) => {
    const user = new User({
      name: req.body.name,
      email: req.body.email,
      age: req.body.age
    })
    await user.save()
    return res.status(201).send({ message: "Usuário criado com sucesso!", user })
  })


// DELETE:
// --> Vai remover dados
app.delete('/:id', async (req, res) => {
    const user = await User.findByIdAndDelete(req.params.id)
    return res.status(200).send({ message: "Usuário deletado.", user})
})


// PUT:
// --> Vai atualziar dados (update)
app.put('/:id', async (req, res) => {
    const user =  await User.findByIdAndUpdate(req.params.id, { 
      name: req.body.name,
      email: req.body.email,
      age: req.body.age
    })

    return res.status(200).send({ message: "Dados atualizados", user})
})


// PATCH:
// --> Vai atualizar apenas os dados fornecidos (update parcial)
app.patch('/:id', async (req, res) => {
  
    const user =  await User.findByIdAndUpdate(req.params.id, { 
      name: req.body.name,
      new: true, fields: { name: 1 }  // --> vai retornar apenas o campo "name"
    })

    return res.send(user);
})


// OPTIONS:
// --> Retorna os métodos HTTP permitidos para a rota '/'
app.options('/', (req, res) => {
  res.set('Allow', 'GET, POST, DELETE, PUT, PATCH, HEAD, OPTIONS');
  return res.sendStatus(204); 
});


//última linha
app.listen(3000, function(){ // *Funçao de callback
    console.log("Seridor sendo excutado na URL: http://localhost:3000")
    mongoose.connect('mongodb+srv://PabloBD:qQR7iIucUVdlf2uO@users.5sird.mongodb.net/?retryWrites=true&w=majority&appName=Users'); //Banco de Dados
}); 

//qQR7iIucUVdlf2uO
```
 ### **Estrutura:** 

![alt text](images/image2.png)

### **Modelo:**

Em outra pasta(`Models`) no arquivo `User.js` foi utilizado um schema, cujo é uma estrutura que define os campos e seus respectivos tipos no documento **MongoDB**. 

O Mongoose é importado em `index.js` para criar o esquema e o modelo de usuário. Ele fornece métodos que tornam o trabalho com documentos no MongoDB mais fácil e organizado.

```javascript
// models/User.js
const mongoose = require('mongoose');

const userSchema = new mongoose.Schema({
  name: String,
  email: String,
  age: Number
});

const User = mongoose.model('User', userSchema);

module.exports = User;

```
O modelo User é criado a partir do esquema. O Mongoose mapeia este modelo para uma coleção no banco de dados (por padrão, ele cria a coleção com o nome pluralizado, como users).<br>

---

User agora possui métodos integrados para realizar operações no banco, como:

- `find()`: Busca todos os documentos;

- `findById()`: Busca por um documento específico pelo ID;

- `save()`: Salva um novo documento;

- `findByIdAndUpdate()`: Atualiza um documento existente;

- `findByIdAndDelete()`: Remove um documento específico.


Enfim, o modelo **User** também é exportado para que possa ser usado em outros arquivos do projeto.

---

### **1.5 Mongo DB:**
Neste projeto, o MongoDB atua como o banco de dados principal, armazenando as informações de usuários em uma coleção. Com o auxílio do Mongoose, é possível realizar operações como criação, leitura, atualização e exclusão de dados de forma eficiente e organizada. 

A conexão com o MongoDB foi estabelecida utilizando o método `mongoose.connect()` na função `app.listen` do servidor. O URI fornecido aponta para um banco de dados hospedado no MongoDB Atlas (uma solução de banco de dados na nuvem):

```javascript
mongoose.connect('mongodb+srv://PabloBD:{password}@users.5sird.mongodb.net/?retryWrites=true&w=majority&appName=Users');
```
E nas imagens a seguir, é possível ver os dados que foram armazenados no banco de dados através do método `POST()`:

<img src= "images/image3.png" alt="alt text" width="700"/>
<img src= "images/image4.png" alt="alt text" width="700"/>

---

---

## 🔗 Links Adicionais
- _[[Backend com NodeJs](https://youtu.be/YeEX1s4M9xg?si=nP5URlBEeVsOGquK)]_
- _[[Curso NodeJs](https://youtu.be/LLqq6FemMNQ?si=Uy3DwCMR9RAVoAS9)]_  
- _[[API com NodeJs](https://youtu.be/PyrMT0GA3sE?si=FpGBO07t-OqV2u7h)]_  
- _[[API com NodeJs](https://youtu.be/zaWFnHagbrM?si=5GbzI_zsUC2M1tzv)]_  
