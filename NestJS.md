# **Registro de Estudo: NestJS**

## 📝 **Tópicos Abordados**
- [NestJS](#nestjs)  
- [Configuração com MongoDB](#mongodb)  
- [Módulos, Controllers e Serviços](#estruturanest)  
- [Rotas RESTful com NestJS](#rotas)  

---

## **1. NestJS <a id="nestjs"></a>**

**NestJS** é um framework Node.js progressivo e moderno que utiliza TypeScript por padrão. Ele é projetado para ser altamente escalável e modular, facilitando o desenvolvimento de APIs robustas e organizadas. Inspirado nos princípios do Angular, utiliza o conceito de **módulos**, **controladores**, e **serviços** para gerenciar diferentes responsabilidades em uma aplicação.

---

## **2. Configuração com MongoDB <a id="mongodb"></a>**

O **NestJS** facilita a integração com bancos de dados como o **MongoDB** através de pacotes como o `@nestjs/mongoose` e `mongoose`. Segue um exemplo de configuração básica para conectar-se ao MongoDB:

### **2.1 Instalação das Dependências**
Execute os seguintes comandos para instalar os pacotes necessários:
```bash
npm install @nestjs/mongoose mongoose
```

### **2.2 Configuração do Módulo de Conexão**
No arquivo principal do módulo da aplicação (app.module.ts), importe o módulo do Mongoose e configure-o com a URI do MongoDB.

```ts
// app.module.ts
import { Module } from '@nestjs/common';
import { MongooseModule } from '@nestjs/mongoose';
import { UsersModule } from './users/users.module';

@Module({
  imports: [
    MongooseModule.forRoot('mongodb+srv://<usuario>:<senha>@cluster.mongodb.net/nestdb'),
    UsersModule,
  ],
  controllers: [],
  providers: [],
})
export class AppModule {}
```

## **3. Módulos, Controllers e Serviços** <a id="estruturanest"></a>
No NestJS, a lógica de negócios e rotas são organizadas em módulos. Segue um exemplo de um módulo simples para gerenciar usuários.

### **3.1 Estrutura do Módulo Users** <br>

```bash
nest generate module users

```

```bash
nest generate controller users

```


```bash
nest generate service users
```

### **3.2 Modelo e Schema do Usuário**
Criamos um modelo User para armazenar os dados no MongoDB. Para isso, usamos o Mongoose Schema:

```ts
// users/schemas/user.schema.ts
import { Prop, Schema, SchemaFactory } from '@nestjs/mongoose';
import { Document } from 'mongoose';

@Schema()
export class User extends Document {
  @Prop({ required: true })
  name: string;

  @Prop({ required: true, unique: true })
  email: string;

  @Prop({ required: true })
  age: number;
}

export const UserSchema = SchemaFactory.createForClass(User);
```

### **3.3 Importando o Schema no Módulo**
```ts
// users/users.module.ts
import { Module } from '@nestjs/common';
import { MongooseModule } from '@nestjs/mongoose';
import { UsersService } from './users.service';
import { UsersController } from './users.controller';
import { User, UserSchema } from './schemas/user.schema';

@Module({
  imports: [
    MongooseModule.forFeature([{ name: User.name, schema: UserSchema }]),
  ],
  controllers: [UsersController],
  providers: [UsersService],
})
export class UsersModule {}

```

## **4. Rotas RESTful com NestJS** <a id="rotas"></a>
No NestJS, criamos as rotas RESTful no controller. Aqui está um exemplo com operações CRUD para gerenciar os usuários:

### **4.1 Controller de Usuários**
```ts
// users/users.controller.ts
import { Controller, Get, Post, Body, Param, Delete, Put } from '@nestjs/common';
import { UsersService } from './users.service';
import { User } from './schemas/user.schema';

@Controller('users')
export class UsersController {
  constructor(private readonly usersService: UsersService) {}

  // GET: Retorna todos os usuários
  @Get()
  async findAll(): Promise<User[]> {
    return this.usersService.findAll();
  }

  // GET: Retorna um usuário pelo ID
  @Get(':id')
  async findOne(@Param('id') id: string): Promise<User> {
    return this.usersService.findOne(id);
  }

  // POST: Cria um novo usuário
  @Post()
  async create(@Body() user: User): Promise<User> {
    return this.usersService.create(user);
  }

  // PUT: Atualiza um usuário pelo ID
  @Put(':id')
  async update(@Param('id') id: string, @Body() user: User): Promise<User> {
    return this.usersService.update(id, user);
  }

  // DELETE: Remove um usuário pelo ID
  @Delete(':id')
  async delete(@Param('id') id: string): Promise<User> {
    return this.usersService.delete(id);
  }
}

```

### **4.2 Serviço para Usuários**
A lógica de negócios fica no service, que utiliza o mongoose para interagir com o MongoDB:

```ts
// users/users.service.ts
import { Injectable } from '@nestjs/common';
import { InjectModel } from '@nestjs/mongoose';
import { Model } from 'mongoose';
import { User } from './schemas/user.schema';

@Injectable()
export class UsersService {
  constructor(@InjectModel(User.name) private userModel: Model<User>) {}

  async findAll(): Promise<User[]> {
    return this.userModel.find().exec();
  }

  async findOne(id: string): Promise<User> {
    return this.userModel.findById(id).exec();
  }

  async create(user: User): Promise<User> {
    const newUser = new this.userModel(user);
    return newUser.save();
  }

  async update(id: string, user: User): Promise<User> {
    return this.userModel.findByIdAndUpdate(id, user, { new: true }).exec();
  }

  async delete(id: string): Promise<User> {
    return this.userModel.findByIdAndDelete(id).exec();
  }
}

```
Repositório API com NestJs: https://github.com/Domingueszz/Nest_API/tree/main
---

---

## 🔗 Links Adicionais
- _[[Curso Express API REST com  NEST +  MONGODB](https://youtu.be/V0ye6gXFFj0?si=QNgd-m0w9vy6W2U2)]_
- _[[NestJs](https://docs.nestjs.com/)]_  
- _[[1- API com NestJs](https://youtu.be/OwmxMNu1Rms?si=GjXtr5xpEeS71fs5)]_  
- _[[2- API com NestJs](https://youtu.be/6xaCQggH7V8?si=I2FRvEntbHBQ-DK2)]_  


