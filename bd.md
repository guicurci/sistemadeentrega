Criando um Banco de Dados Simples com mongoDB
```
use hello_world_db
```
Criando uma Coleção e Inserindo Dados
```
db.mensagens.insertOne({ "mensagem": "Hello, World!" })
```
Consultando os Dados
```
db.mensagens.find().pretty()
```
Deletando um Registro
```
db.mensagens.deleteOne({ "mensagem": "Hello, MongoDB!" })
```
Controle de acesso

Criando autenticação (login)
```
const express = require("express");
const jwt = require("jsonwebtoken");
const bcrypt = require("bcryptjs");
require("dotenv").config();

const app = express();
app.use(express.json());

const users = []; // Simulação de um banco de dados

app.post("/register", async (req, res) => {
    const { username, password, role } = req.body;
    const hashedPassword = await bcrypt.hash(password, 10);
    users.push({ username, password: hashedPassword, role });
    res.json({ message: "Usuário registrado!" });
});

app.post("/login", async (req, res) => {
    const { username, password } = req.body;
    const user = users.find(u => u.username === username);
    if (!user || !(await bcrypt.compare(password, user.password))) {
        return res.status(401).json({ message: "Credenciais inválidas!" });
    }

    const token = jwt.sign({ username, role: user.role }, process.env.JWT_SECRET, { expiresIn: "1h" });
    res.json({ token });
});
```
Criando Middleware para proteger rotas
```
const authenticateJWT = (req, res, next) => {
    const token = req.header("Authorization")?.split(" ")[1];
    if (!token) return res.status(403).json({ message: "Acesso negado!" });

    jwt.verify(token, process.env.JWT_SECRET, (err, user) => {
        if (err) return res.status(403).json({ message: "Token inválido!" });
        req.user = user;
        next();
    });
};
```
Criando rotas protegidas por permissões
```
app.get("/admin", authenticateJWT, (req, res) => {
    if (req.user.role !== "admin") return res.status(403).json({ message: "Acesso restrito!" });
    res.json({ message: "Bem-vindo, administrador!" });
});

app.listen(3000, () => console.log("Servidor rodando na porta 3000"));
