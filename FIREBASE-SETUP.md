# 🔥 Configuração do Firebase para Renova Lotes

## 📋 Índice
1. [Criação do Projeto Firebase](#1-criação-do-projeto-firebase)
2. [Configuração do Firestore](#2-configuração-do-firestore)
3. [Configuração do Código](#3-configuração-do-código)
4. [Estrutura do Firestore](#4-estrutura-do-firestore)
5. [Regras de Segurança](#5-regras-de-segurança)
6. [Testando a Integração](#6-testando-a-integração)

---

## 1. Criação do Projeto Firebase

### Passo 1: Acessar o Console Firebase
1. Acesse: https://console.firebase.google.com/
2. Clique em **"Adicionar projeto"** ou **"Add project"**

### Passo 2: Configurar o Projeto
1. **Nome do projeto**: Digite `renova-lotes` (ou o nome que preferir)
2. **Google Analytics**: Pode desabilitar se não quiser análise de uso
3. Clique em **"Criar projeto"**
4. Aguarde a criação (leva cerca de 30 segundos)

### Passo 3: Registrar o App Web
1. No painel do projeto, clique no ícone **Web** `</>`
2. **Nome do app**: Digite `Renova Lotes Dashboard`
3. **NÃO** marque "Configurar Firebase Hosting"
4. Clique em **"Registrar app"**
5. **COPIE** o objeto de configuração que aparece (firebaseConfig)

---

## 2. Configuração do Firestore

### Passo 1: Criar o Banco de Dados
1. No menu lateral, clique em **"Firestore Database"**
2. Clique em **"Criar banco de dados"**
3. Escolha o modo:
   - **Modo de produção** (recomendado para uso real)
   - **Modo de teste** (apenas para desenvolvimento - dados ficam públicos)
4. Escolha a **localização**: `southamerica-east1 (São Paulo)` para menor latência no Brasil
5. Clique em **"Ativar"**

### Passo 2: Criar as Collections Manualmente (Opcional)
O código cria automaticamente, mas você pode criar manualmente:

1. Clique em **"Iniciar coleção"**
2. Crie as seguintes collections:
   - `configuracoes` (com documento ID: `config`)
   - `items` (deixe vazia, será populada automaticamente)
   - `lixeira` (com documento ID: `trash`)

---

## 3. Configuração do Código

### Passo 1: Localizar a Configuração no HTML
Abra o arquivo `index3.html` e procure por:

```javascript
const FIREBASE_CONFIG = {
  // SUBSTITUA COM SUAS CREDENCIAIS DO FIREBASE
  apiKey: "SUA_API_KEY",
  authDomain: "SEU_PROJECT_ID.firebaseapp.com",
  projectId: "SEU_PROJECT_ID",
  storageBucket: "SEU_PROJECT_ID.appspot.com",
  messagingSenderId: "SEU_MESSAGING_SENDER_ID",
  appId: "SEU_APP_ID"
};
```

### Passo 2: Substituir com Suas Credenciais
Cole as credenciais que você copiou do Firebase Console. Exemplo:

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

### Passo 3: Salvar e Testar
1. Salve o arquivo `index3.html`
2. Abra o arquivo no navegador
3. Abra o **Console do Navegador** (F12 → Console)
4. Você deve ver: `✅ Firebase inicializado com sucesso!`

---

## 4. Estrutura do Firestore

### 📦 Collection: `configuracoes`
**Documento ID**: `config`

**Estrutura**:
```json
{
  "canaisAnuncio": ["Shopee", "Mercado Livre", "OLX", "Instagram", "Facebook"],
  "canaisVenda": ["Shopee", "Mercado Livre", "Instagram", "Venda Direta"],
  "entregas": ["Correios", "Motoboy", "Retirada", "Transportadora"],
  "tiposProduto": ["Eletrônico", "Roupa", "Calçado", "Acessório", "Outros"],
  "locaisEstoque": ["Estoque Principal", "Casa", "Depósito"],
  "vendedores": ["João", "Maria", "Sistema"],
  "lots": [
    {
      "id": "lote-1",
      "nome": "Lote 1",
      "total": 6050.00,
      "criadoEm": "2026-01-15"
    }
  ]
}
```

**Campos**:
- `canaisAnuncio`: Array de strings - Canais onde produtos são anunciados
- `canaisVenda`: Array de strings - Canais de venda
- `entregas`: Array de strings - Métodos de entrega disponíveis
- `tiposProduto`: Array de strings - Categorias de produtos
- `locaisEstoque`: Array de strings - Locais de armazenamento
- `vendedores`: Array de strings - Vendedores cadastrados
- `lots`: Array de objetos - Lotes de compra com:
  - `id`: String única identificadora
  - `nome`: Nome do lote
  - `total`: Valor total do lote (number)
  - `criadoEm`: Data de criação (ISO string)

---

### 📦 Collection: `items`
**Documentos**: Um por item, ID gerado automaticamente (ex: `item-1234567890`)

**Estrutura de cada documento**:
```json
{
  "id": "item-1234567890",
  "codigo": "COD001",
  "produto": "Smartphone Samsung A54",
  "categoria": "Eletrônico",
  "loteId": "lote-1",
  "custoProduto": 303.50,
  "vendido": true,
  "valorAnuncio": 850.00,
  "valorVenda": 800.00,
  "custoEntrega": 20.00,
  "localEstoque": "Estoque Principal",
  "dataAnuncio": "2026-02-01",
  "dataVenda": "2026-02-15",
  "canaisAnuncio": ["Shopee", "Instagram"],
  "canalVenda": "Shopee",
  "entrega": "Correios",
  "vendedor": "João",
  "observacoes": "Cliente pediu nota fiscal",
  "pagamento": {
    "tipo": "parcelado",
    "parcelas": [
      { "data": "2026-02-15", "valor": 400.00 },
      { "data": "2026-03-15", "valor": 400.00 }
    ]
  },
  "photos": ["data:image/jpeg;base64,...", "", ""]
}
```

**Campos Principais**:
- `id`: String única (gerada automaticamente)
- `codigo`: Código do produto
- `produto`: Nome/descrição do produto
- `categoria`: Tipo de produto
- `loteId`: ID do lote ao qual pertence
- `custoProduto`: Custo rateado do produto (calculado automaticamente)
- `vendido`: Boolean - se foi vendido
- `valorAnuncio`: Valor anunciado
- `valorVenda`: Valor efetivamente vendido
- `custoEntrega`: Custo de envio
- `localEstoque`: Local de armazenamento
- `dataAnuncio`: Data do anúncio (ISO date string)
- `dataVenda`: Data da venda (ISO date string)
- `canaisAnuncio`: Array - canais onde foi anunciado
- `canalVenda`: String - canal onde foi vendido
- `entrega`: Método de entrega usado
- `vendedor`: Vendedor responsável
- `observacoes`: Notas adicionais
- `pagamento`: Objeto com informações de pagamento
  - `tipo`: "avista" | "parcelado" | "outro"
  - `parcelas`: Array de objetos (data e valor)
- `photos`: Array com até 3 fotos (base64 ou URLs)

---

### 📦 Collection: `lixeira`
**Documento ID**: `trash`

**Estrutura**:
```json
{
  "items": [
    {
      "deletedAt": "2026-02-20T14:30:00.000Z",
      "item": {
        // Estrutura completa do item (mesmo formato de "items")
      }
    }
  ]
}
```

**Campos**:
- `items`: Array de objetos deletados
  - `deletedAt`: Data/hora da exclusão (ISO string)
  - `item`: Objeto completo do item excluído

**Regra**: Itens com mais de 30 dias são automaticamente removidos

---

## 5. Regras de Segurança

### ⚠️ IMPORTANTE: Configurar Regras de Segurança

Por padrão, o Firestore em modo de teste permite acesso público. Para produção, você **DEVE** configurar regras de segurança.

### Passo 1: Acessar Regras
1. No Firebase Console, vá em **Firestore Database**
2. Clique na aba **"Regras"** (Rules)

### Passo 2: Regras Básicas (Acesso Público - NÃO RECOMENDADO)
```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} {
      allow read, write: if true;
    }
  }
}
```
**⚠️ ATENÇÃO**: Estas regras permitem acesso total. Use apenas para teste!

### Passo 3: Regras Recomendadas (Com Autenticação)
Para produção, configure Firebase Authentication e use:

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // Apenas usuários autenticados podem acessar
    match /configuracoes/{document} {
      allow read, write: if request.auth != null;
    }
    
    match /items/{itemId} {
      allow read, write: if request.auth != null;
    }
    
    match /lixeira/{document} {
      allow read, write: if request.auth != null;
    }
  }
}
```

### Passo 4: Regras Avançadas (Multi-usuário)
Para permitir que cada usuário veja apenas seus dados:

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId}/configuracoes/{document} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
    
    match /users/{userId}/items/{itemId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
    
    match /users/{userId}/lixeira/{document} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```

**Nota**: Para usar regras multi-usuário, você precisará adaptar o código para incluir o `userId` no caminho das collections.

---

## 6. Testando a Integração

### ✅ Checklist de Teste

1. **Abrir o arquivo no navegador**
   - Abra `index3.html` no Chrome/Edge/Firefox
   
2. **Verificar Console (F12)**
   - Deve aparecer: `✅ Firebase inicializado com sucesso!`
   - Deve aparecer: `✅ Dados carregados do Firebase com sucesso!`

3. **Criar um novo item**
   - Clique em **"Novo item"**
   - Preencha os dados
   - Clique em **"Salvar"**
   - Verifique no console: `✅ Salvo: items/item-...`

4. **Verificar no Firebase Console**
   - Acesse o Firebase Console
   - Vá em **Firestore Database**
   - Verifique se a collection `items` foi criada
   - Verifique se o documento do item está lá

5. **Testar sincronização**
   - Limpe o cache do navegador (Ctrl+Shift+Delete)
   - Recarregue a página
   - Os dados devem ser carregados do Firebase
   - Verifique no console: `✅ Carregados X documentos de items`

---

## 📊 Funcionamento da Sincronização

### Fluxo de Salvamento:
```
1. Usuário clica em "Salvar"
2. Dados são validados
3. Salvamento em localStorage (backup local instantâneo)
4. Salvamento no Firebase (assíncrono, não bloqueia interface)
5. Console mostra confirmação
```

### Fluxo de Carregamento:
```
1. Página carrega
2. Firebase é inicializado
3. Dados do localStorage são carregados (funcionamento offline)
4. Dados do Firebase são carregados (assíncrono)
5. Firebase sobrescreve localStorage se houver dados mais recentes
6. Interface é atualizada
```

### Vantagens desta Abordagem:
- ✅ **Funciona offline**: localStorage como fallback
- ✅ **Performance**: Não bloqueia a interface
- ✅ **Sincronização**: Dados sempre atualizados entre dispositivos
- ✅ **Backup automático**: Dados seguros na nuvem
- ✅ **Escalável**: Suporta múltiplos dispositivos

---

## 🔧 Solução de Problemas

### Erro: "Firebase not defined"
- **Causa**: Scripts do Firebase não carregaram
- **Solução**: Verifique se tem internet e se os scripts no `<head>` estão corretos

### Erro: "Permission denied"
- **Causa**: Regras de segurança bloqueando acesso
- **Solução**: Configure as regras no Firebase Console (seção 5)

### Aviso: "Firebase não configurado"
- **Causa**: Credenciais não foram substituídas
- **Solução**: Substitua os valores em `FIREBASE_CONFIG` com suas credenciais reais

### Dados não aparecem após reload
- **Causa**: Erro no carregamento do Firebase
- **Solução**: 
  1. Abra o Console (F12)
  2. Verifique se há erros em vermelho
  3. Verifique se as regras de segurança estão corretas

### Lentidão ao salvar
- **Causa**: Muitas fotos grandes em base64
- **Solução**: 
  1. Reduza o tamanho das imagens antes de salvar
  2. Considere usar Firebase Storage para fotos grandes
  3. Comprima as imagens (max 800x800px)

---

## 🚀 Próximos Passos Opcionais

### 1. Adicionar Firebase Authentication
Para ter login de usuários e dados privados:
- Configure Firebase Authentication no console
- Adicione botões de login/logout no HTML
- Adapte as regras de segurança

### 2. Usar Firebase Storage para Fotos
Para otimizar armazenamento de imagens:
- Configure Firebase Storage
- Faça upload das fotos para Storage
- Salve apenas as URLs no Firestore

### 3. Implementar Sincronização em Tempo Real
Para ver alterações instantâneas:
- Use `onSnapshot()` ao invés de `.get()`
- Atualize a interface quando dados mudarem
- Ideal para múltiplos usuários editando simultaneamente

### 4. Adicionar Backup Automático
- Configure Cloud Functions para backup periódico
- Exporte dados automaticamente para Cloud Storage
- Configure alertas de falha

---

## 📞 Suporte

Em caso de dúvidas:
1. Consulte a documentação oficial: https://firebase.google.com/docs/firestore
2. Verifique o console do navegador (F12) para erros
3. Teste com regras de segurança no modo teste primeiro
4. Certifique-se de que todas as credenciais estão corretas

---

**✅ Configuração Completa!**

Agora seu sistema está salvando dados no Firebase Firestore de forma segura e automática.
