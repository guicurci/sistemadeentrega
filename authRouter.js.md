Este arquivo define as rotas de autenticação do sistema, permitindo:

Cadastro de usuários (POST /register)
Login de usuários (POST /login)
O sistema usa bcrypt.js para criptografar senhas e jsonwebtoken (JWT) para gerar um token de autenticação.
```
pasta: routes/authRoutes.js
```
```
// Importação dos módulos necessários
const express = require("express");
const bcrypt = require("bcryptjs"); // Para criptografar senhas
const jwt = require("jsonwebtoken"); // Para gerar tokens de autenticação
const User = require("../models/User"); // Modelo do usuário

const router = express.Router(); // Criando o roteador Express
```
// Rota de Cadastro de Usuário
```
router.post("/register", async (req, res) => {
  const { nome, email, cpf, senha } = req.body;

  try {
    // 🔹 Verificar se o usuário já existe no banco de dados
    let user = await User.findOne({ email });
    if (user) return res.status(400).json({ msg: "Usuário já existe" });

    // 🔹 Criptografar a senha antes de salvar no banco
    const salt = await bcrypt.genSalt(10);
    const senhaHash = await bcrypt.hash(senha, salt);

    // 🔹 Criar novo usuário no MongoDB
    user = new User({ nome, email, cpf, senha: senhaHash });
    await user.save();

    res.status(201).json({ msg: "Usuário cadastrado com sucesso" });
  } catch (err) {
    res.status(500).send("Erro no servidor");
  }
});
```
// Rota de Login do Usuário
```
router.post("/login", async (req, res) => {
  const { email, senha } = req.body;

  try {
    // Verificar se o usuário existe no banco de dados
    let user = await User.findOne({ email });
    if (!user) return res.status(400).json({ msg: "Usuário não encontrado" });

    // Comparar a senha digitada com a senha salva no banco
    const isMatch = await bcrypt.compare(senha, user.senha);
    if (!isMatch) return res.status(400).json({ msg: "Senha incorreta" });

    // Gerar um token JWT para autenticação do usuário
    const token = jwt.sign({ id: user.id }, process.env.JWT_SECRET, { expiresIn: "1h" });

    res.json({ token, user: { id: user.id, nome: user.nome, email: user.email } });
  } catch (err) {
    res.status(500).send("Erro no servidor");
  }
});
```
// Exportando o roteador para ser usado no servidor principal
```
module.exports = router;
```
