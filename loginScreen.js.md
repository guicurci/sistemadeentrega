Este componente implementa uma tela de login em um aplicativo React Native. 
Ele: Permite que o usuário insira e-mail e senha.
Envia os dados para o backend e verifica as credenciais.
Armazena o token JWT no AsyncStorage após o login.
Redireciona o usuário para a tela principal (Home).
```
pasta: screens/LoginScreen.js
```
Importação de pacotes essenciais do React Native e bibliotecas externas
```
import React, { useState } from "react";
import { View, Text, TextInput, Button, Alert } from "react-native";
import axios from "axios"; // Para requisições HTTP
import AsyncStorage from "@react-native-async-storage/async-storage"; // Para armazenamento local
```
Componente de Login
```
const LoginScreen = ({ navigation }) => {
  // Estados para armazenar o e-mail e senha digitados pelo usuário
  const [email, setEmail] = useState("");
  const [senha, setSenha] = useState("");

  //Função para realizar o login
  const handleLogin = async () => {
    try {
      // Enviar credenciais para a API
      const res = await axios.post("http://seu-servidor:5000/api/auth/login", { email, senha });

      // Armazenar o token JWT no AsyncStorage
      await AsyncStorage.setItem("token", res.data.token);

      // Exibir mensagem de sucesso
      Alert.alert("Login realizado!");

      // Redirecionar para a tela inicial
      navigation.navigate("Home");
    } catch (err) {
      // Exibir alerta de erro caso as credenciais estejam incorretas
      Alert.alert("Erro ao fazer login", err.response?.data?.msg || "Erro desconhecido");
    }
  };

  return (
    <View>
      {/* Campo de entrada para o e-mail */}
      <Text>E-mail:</Text>
      <TextInput 
        value={email} 
        onChangeText={setEmail} 
        autoCapitalize="none" // Impede a capitalização automática do e-mail
        keyboardType="email-address" // Define o teclado apropriado para e-mail
      />
      
      {/* Campo de entrada para a senha */}
      <Text>Senha:</Text>
      <TextInput 
        value={senha} 
        onChangeText={setSenha} 
        secureTextEntry // Oculta a senha
      />
      
      {/* Botão para realizar o login */}
      <Button title="Entrar" onPress={handleLogin} />
      
      {/* Botão para navegar para a tela de cadastro */}
      <Button title="Cadastre-se" onPress={() => navigation.navigate("Register")} />
    </View>
  );
};
```

// Exporta o componente para ser utilizado no aplicativo
```
export default LoginScreen;
```
