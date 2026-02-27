# 🚀 Guia Rápido - Configuração Firebase

## ⚡ Configuração em 5 Minutos

### 1️⃣ Criar Projeto Firebase
```
1. Acesse: https://console.firebase.google.com/
2. Clique em "Adicionar projeto"
3. Nome: renova-lotes
4. Desabilite Google Analytics (opcional)
5. Clique em "Criar projeto"
```

### 2️⃣ Configurar Firestore
```
1. Menu lateral → "Firestore Database"
2. Clique em "Criar banco de dados"
3. Modo: "Modo de teste" (para começar)
4. Localização: "southamerica-east1 (São Paulo)"
5. Clique em "Ativar"
```

### 3️⃣ Obter Credenciais
```
1. Clique no ícone de engrenagem → "Configurações do projeto"
2. Role até "Seus apps"
3. Clique no ícone Web (</>)
4. Nome do app: "Renova Lotes"
5. COPIE o objeto firebaseConfig
```

### 4️⃣ Configurar o Código
Abra `index3.html` e procure por:
```javascript
const FIREBASE_CONFIG = {
```

**COLE suas credenciais** no lugar dos valores `"SUA_API_KEY"`, etc.

Exemplo do que você vai colar:
```javascript
const FIREBASE_CONFIG = {
  apiKey: "AIzaSyC1234567890abcdefghijklmnopqrst",
  authDomain: "renova-lotes-12345.firebaseapp.com",
  projectId: "renova-lotes-12345",
  storageBucket: "renova-lotes-12345.appspot.com",
  messagingSenderId: "123456789012",
  appId: "1:123456789012:web:abcdef1234567890abcdef"
};
```

### 5️⃣ Testar
```
1. Salve o arquivo index3.html
2. Abra no navegador
3. Pressione F12 (Console)
4. Deve aparecer: ✅ Firebase inicializado com sucesso!
5. Crie um novo item e salve
6. Verifique no Firebase Console se apareceu na collection "items"
```

---

## 📊 Estrutura das Collections no Firestore

### Collection: `configuracoes`
- **Documento único**: `config`
- **Contém**: Listas de opções e lotes

### Collection: `items`
- **Múltiplos documentos**: Um por produto
- **ID automático**: `item-1234567890`
- **Contém**: Todos os dados do produto

### Collection: `lixeira`
- **Documento único**: `trash`
- **Contém**: Array de itens excluídos (últimos 30 dias)

---

## ⚠️ IMPORTANTE: Regras de Segurança

### Para TESTE (30 dias):
```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} {
      allow read, write: if request.time < timestamp.date(2026, 4, 1);
    }
  }
}
```

### Para PRODUÇÃO (recomendado):
```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} {
      allow read, write: if request.auth != null;
    }
  }
}
```

**Como aplicar**:
1. Firebase Console → Firestore Database → Regras
2. Cole o código acima
3. Clique em "Publicar"

---

## ✅ Checklist Pós-Configuração

- [ ] Projeto Firebase criado
- [ ] Firestore ativado (modo teste ou produção)
- [ ] Credenciais copiadas para `FIREBASE_CONFIG`
- [ ] Arquivo salvo e aberto no navegador
- [ ] Console mostra "Firebase inicializado"
- [ ] Item de teste criado e salvo
- [ ] Item aparece no Firebase Console
- [ ] Regras de segurança configuradas

---

## 🔧 Solução Rápida de Problemas

| Problema | Solução |
|----------|---------|
| "Firebase not defined" | Verifique se tem internet |
| "Permission denied" | Configure as regras de segurança |
| Dados não aparecem | Abra F12 e veja os erros em vermelho |
| "Firebase não configurado" | Substitua as credenciais em FIREBASE_CONFIG |

---

## 🎯 O que Foi Alterado no Código

### ✅ Adicionado no `<head>`:
```html
<script src="https://www.gstatic.com/firebasejs/10.8.0/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/10.8.0/firebase-firestore-compat.js"></script>
```

### ✅ Novas Funções JavaScript:
- `initFirebase()` - Inicializa conexão
- `saveToFirestore()` - Salva documentos
- `loadFromFirestore()` - Carrega documentos
- `loadCollectionFromFirestore()` - Carrega collections
- `deleteFromFirestore()` - Remove documentos
- `loadFromFirebaseOnBoot()` - Sincroniza ao iniciar

### ✅ Funções Atualizadas:
- `saveItems()` - Agora salva no Firebase + localStorage
- `saveConfig()` - Agora salva no Firebase + localStorage
- `saveTrash()` - Agora salva no Firebase + localStorage

### ✅ Comportamento:
- **Salvamento**: localStorage primeiro (rápido) → Firebase depois (nuvem)
- **Carregamento**: localStorage primeiro (offline) → Firebase sobrescreve (atualizado)
- **Vantagem**: Funciona offline e sincroniza quando conectado

---

## 📱 Testando Multi-Dispositivo

1. Configure o Firebase no primeiro dispositivo
2. Crie alguns itens
3. Abra o mesmo arquivo em outro navegador/dispositivo
4. **Configure com as MESMAS credenciais**
5. Os dados devem aparecer automaticamente

---

## 💡 Dica Final

> Mantenha suas credenciais do Firebase em local seguro!
> 
> Não compartilhe seu `apiKey` publicamente se estiver usando regras de segurança restritas.

Para uso profissional, considere:
- Adicionar Firebase Authentication
- Usar variáveis de ambiente
- Implementar backup automático

---

**Pronto! Seu sistema está na nuvem! ☁️✨**

Consulte `FIREBASE-SETUP.md` para documentação completa.
