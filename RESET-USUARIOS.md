# 🔑 Resetar Usuários Firebase

Como os usuários existem mas as senhas podem estar erradas, faça o seguinte:

## Opção 1: Resetar Senha do Usuário Existente

1. Acesse: https://console.firebase.google.com/u/5/project/paleta-yasmim/authentication/users

2. Clique em um dos usuários (ex: `luiz@paletamarketing.com.br`)

3. Clique em **Redefinir senha**

4. Firebase enviará um email com link para redefinir

5. Use a nova senha no app

---

## Opção 2: Criar Novo Usuário de Teste

1. Acesse: https://console.firebase.google.com/u/5/project/paleta-yasmim/authentication/users

2. Clique no botão **+ Adicionar usuário** (canto superior direito)

3. Preencha:
   - **Email:** `teste@paletamarketing.com.br`
   - **Senha:** `Teste123456`
   - Clique em **Adicionar usuário**

4. Depois, em Firestore, crie o documento do usuário:
   - Abra: https://console.firebase.google.com/u/5/project/paleta-yasmim/firestore/data
   - Coleção: `users`
   - Documento ID: (copie o UID do usuário criado acima)
   - Campos:
     ```
     email: "teste@paletamarketing.com.br"
     tenantId: "yasmim-meneses"
     role: "admin"
     displayName: "Usuário Teste"
     createdAt: (data atual)
     updatedAt: (data atual)
     ```

5. Teste com:
   - Email: `teste@paletamarketing.com.br`
   - Senha: `Teste123456`

---

## Verificar Tenants

Verifique também que o tenant `yasmim-meneses` existe em Firestore:
- Coleção: `tenants`
- Documento: `yasmim-meneses`

Se não existir, crie com:
```
name: "Dra. Yasmim Meneses"
slug: "yasmim-meneses"
plan: "pro"
status: "active"
createdAt: (data atual)
updatedAt: (data atual)
```

---

## Depois de Tudo Pronto

Volte para: https://calendarioeditorial.paletamarketing.com.br e teste!
