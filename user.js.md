Este arquivo define o esquema do usuário no banco de dados MongoDB utilizando o Mongoose. Ele estrutura como os dados dos usuários serão armazenados e garante regras como obrigatoriedade e unicidade de alguns campos.


Importa o mongoose para interagir com o banco de dados MongoDB
```
const mongoose = require("mongoose");
```
Define o esquema do usuário
```
const UserSchema = new mongoose.Schema({
  nome: { type: String, required: true }, // Nome do usuário (obrigatório)
  email: { type: String, required: true, unique: true }, // E-mail único e obrigatório
  cpf: { type: String, required: true, unique: true }, // CPF único e obrigatório
  senha: { type: String, required: true }, // Senha obrigatória (armazenada de forma segura)
  tipo_usuario: { 
    type: String, 
    enum: ["admin", "cliente"], // Define os tipos de usuário permitidos
    default: "cliente" // Por padrão, o usuário será "cliente"
  },
  data_cadastro: { type: Date, default: Date.now } // Data de criação do usuário
});
```
Exporta o modelo 'User', baseado no esquema definido
```
module.exports = mongoose.model("User", UserSchema);
```

Caso queira criar um novo usuário, basta importar o modelo no seu backend:

```
const User = require("./models/User");

// Criar novo usuário
const novoUsuario = new User({
  nome: "João Silva",
  email: "joao@email.com",
  cpf: "12345678900",
  senha: "senhaSegura123"
});

novoUsuario.save()
  .then(() => console.log("Usuário cadastrado!"))
  .catch(err => console.log("Erro ao cadastrar:", err));
```
