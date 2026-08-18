# 🔐 Setup Firebase - Paleta Editorial Platform

## Atualizar Regras de Firestore

Para corrigir o erro `Missing or insufficient permissions`, você precisa atualizar as regras de Firestore:

### Passo 1: Acessar Firebase Console
1. Abra https://console.firebase.google.com/u/5/project/paleta-yasmim/firestore/rules
2. Clique em **EDITAR REGRAS** (aba Regras)

### Passo 2: Copiar as Regras
Copie o conteúdo do arquivo `firestore-rules.txt` deste repositório (veja o conteúdo abaixo).

### Passo 3: Colar e Publicar
1. Apague o conteúdo atual
2. Cole as novas regras
3. Clique em **PUBLICAR**

---

## 📝 Regras de Firestore (Copiar e Colar)

```firestore
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {

    function isAuth() {
      return request.auth != null;
    }

    function getUserData() {
      return get(/databases/$(database)/documents/users/$(request.auth.uid)).data;
    }

    function userTenant() {
      let userData = getUserData();
      return userData.tenantId != null ? userData.tenantId : '';
    }

    function belongsToUserTenant(tenantId) {
      return isAuth() && userTenant() == tenantId;
    }

    function hasRole(role) {
      return isAuth() && getUserData().role == role;
    }

    function isEditor() {
      let userRole = getUserData().role;
      return userRole == 'admin' || userRole == 'editor';
    }

    // Users collection - each user can only read/write their own document
    match /users/{userId} {
      allow read: if request.auth.uid == userId;
      allow create: if request.auth.uid == userId;
      allow update: if request.auth.uid == userId;
    }

    // Tenants collection - read if user belongs to tenant, write if admin
    match /tenants/{tenantId} {
      allow read: if isAuth() && belongsToUserTenant(tenantId);
      allow create: if isAuth() && hasRole('admin');
      allow update: if isAuth() && belongsToUserTenant(tenantId) && hasRole('admin');
      allow delete: if isAuth() && belongsToUserTenant(tenantId) && hasRole('admin');
    }

    // Posts collection - filtered by tenantId
    match /posts/{postId} {
      allow read: if isAuth() && belongsToUserTenant(resource.data.tenantId);
      allow create: if isAuth() && belongsToUserTenant(request.resource.data.tenantId) && isEditor();
      allow update: if isAuth() && belongsToUserTenant(resource.data.tenantId) && isEditor();
      allow delete: if isAuth() && belongsToUserTenant(resource.data.tenantId) && hasRole('admin');
    }
  }
}
```

---

## ✅ Após Publicar as Regras

1. Aguarde 1-2 minutos para as regras serem aplicadas
2. Recarregue o app: `https://calendarioeditorial.drayasmim.paletamarketing.com.br`
3. Faça login com suas credenciais
4. Tudo deve funcionar normalmente!

---

## 📊 O que as Regras Fazem

- **Users**: Cada usuário só pode ler/escrever seu próprio documento
- **Tenants**: Usuários só podem ler tenants aos quais pertencem
- **Posts**: Usuários só podem ver posts do seu tenant
- **Roles**: `admin` pode gerenciar; `editor` pode criar/editar posts; `viewer` só lê

---

## 🆘 Se ainda der erro

1. Verifique se o usuário existe em `/users/{uid}`
2. Verifique se o campo `tenantId` está preenchido
3. Verifique se o tenant existe em `/tenants/{tenantId}`
4. Revise o console do navegador para mais detalhes do erro
