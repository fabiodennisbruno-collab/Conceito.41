# 💡 Exemplos Práticos - Firebase Firestore

## 🎯 Casos de Uso Comuns

### 1. Criando seu Primeiro Item

**Passo a passo**:
```
1. Abra index3.html no navegador
2. Clique em "Novo item"
3. Preencha:
   - Código: COD001
   - Produto: Smartphone Samsung A54
   - Categoria: Eletrônico
   - Lote: Lote 1
   - Local: Estoque Principal
4. Clique em "Salvar"
```

**O que acontece nos bastidores**:
```javascript
// 1. Dados validados
const item = {
  id: "item-1708534567890",
  codigo: "COD001",
  produto: "Smartphone Samsung A54",
  categoria: "Eletrônico",
  loteId: "lote-1",
  custoProduto: 303.50,  // Calculado automaticamente
  vendido: false,
  localEstoque: "Estoque Principal",
  // ... outros campos
};

// 2. Salvo no localStorage
localStorage.setItem("renovaLotes:items", JSON.stringify([item]));

// 3. Enviado ao Firebase
db.collection("items").doc("item-1708534567890").set(item);

// Console mostra:
// ✅ Salvo: items/item-1708534567890
```

---

### 2. Registrando uma Venda

**Cenário**: Você vendeu o smartphone por R$ 800 parcelado em 2x

**Passo a passo**:
```
1. Encontre o item na lista
2. Clique no código ou "Editar"
3. Mude "Vendido" para "Sim"
4. Preencha:
   - Valor de venda: 800.00
   - Data da venda: 15/02/2026
   - Canal de venda: Shopee
   - Entrega: Correios (R$ 20)
   - Vendedor: João
   - Tipo pagamento: Parcelado
   - Adicione 2 parcelas:
     * 15/02/2026 - R$ 400
     * 15/03/2026 - R$ 400
5. Clique em "Salvar"
```

**Firebase recebe**:
```javascript
{
  id: "item-1708534567890",
  codigo: "COD001",
  produto: "Smartphone Samsung A54",
  vendido: true,
  valorVenda: 800.00,
  dataVenda: "2026-02-15",
  canalVenda: "Shopee",
  entrega: "Correios",
  custoEntrega: 20.00,
  vendedor: "João",
  pagamento: {
    tipo: "parcelado",
    parcelas: [
      { data: "2026-02-15", valor: 400.00 },
      { data: "2026-03-15", valor: 400.00 }
    ]
  }
}
```

**Lucro calculado automaticamente**:
```
Valor de venda: R$ 800,00
- Custo produto: R$ 303,50 (rateio do lote)
- Custo entrega: R$ 20,00
= Lucro: R$ 476,50 (60% de margem)
```

---

### 3. Gerenciando Múltiplos Lotes

**Cenário**: Você comprou um novo lote

**Passo a passo**:
```
1. Vá para a aba "Configurações"
2. Na seção "Lotes", preencha:
   - Nome: Lote Fevereiro 2026
   - Total: R$ 8500,00
3. Clique em "Adicionar lote"
```

**Firebase atualiza**:
```javascript
// Documento: configuracoes/config
{
  lots: [
    {
      id: "lote-1",
      nome: "Lote 1",
      total: 6050.00,
      criadoEm: "2026-01-15"
    },
    {
      id: "lote-2",
      nome: "Lote Fevereiro 2026",
      total: 8500.00,
      criadoEm: "2026-02-27"
    }
  ]
}
```

**Rateio automático**:
```
Lote 1: 20 produtos × R$ 302,50 cada
Lote 2: 0 produtos (ainda vazio)
```

Ao adicionar produtos ao Lote 2:
```
Lote 2: 25 produtos × R$ 340,00 cada
(8500 ÷ 25 = 340)
```

---

### 4. Adicionando Fotos aos Produtos

**Passo a passo**:
```
1. Edite um item
2. Role até "Fotos do produto"
3. Clique em "Escolher arquivo"
4. Selecione uma imagem (JPG/PNG)
5. A imagem aparece instantaneamente
6. Repita para até 3 fotos
7. Clique em "Salvar"
```

**Firebase armazena**:
```javascript
{
  id: "item-1708534567890",
  photos: [
    "data:image/jpeg;base64,/9j/4AAQSkZJRgABAQEAYABgAAD...",
    "data:image/jpeg;base64,/9j/4AAQSkZJRgABAQEAYABgAAD...",
    ""
  ]
}
```

**Tamanho**:
- Foto original: 2 MB
- Após compressão: ~150 KB
- Total (3 fotos): ~450 KB

**⚠️ Atenção**:
- Máximo 1 MB por documento no Firestore
- Recomendado: Redimensionar para 800x800px
- Para fotos maiores: Use Firebase Storage

---

### 5. Sincronizando Entre Dispositivos

**Cenário**: Você trabalha em 2 computadores

**Computador 1 (Casa)**:
```
1. Configure Firebase (cole credenciais)
2. Crie 10 produtos
3. Feche o navegador
```

**Computador 2 (Trabalho)**:
```
1. Abra o mesmo arquivo index3.html
2. Configure Firebase (MESMAS credenciais)
3. Abra a página
4. Console mostra: ✅ Carregados 10 documentos de items
5. Todos os 10 produtos aparecem automaticamente!
```

**Atualizações em tempo real**:
```
Se você editar no Computador 1:
❌ Computador 2 NÃO atualiza automaticamente
    (precisa recarregar a página)

Para atualização automática:
✅ Use onSnapshot() ao invés de get()
   (requer modificação do código)
```

---

### 6. Trabalhando Offline

**Cenário**: Sem internet

**O que funciona**:
```
✅ Visualizar todos os dados
✅ Criar novos itens
✅ Editar itens existentes
✅ Excluir itens
✅ Exportar JSON/CSV
✅ Buscar e filtrar
```

**O que NÃO funciona**:
```
❌ Sincronizar com outros dispositivos
❌ Backup automático
```

**Quando a internet voltar**:
```
1. Recarregue a página
2. Sistema detecta conexão
3. Dados locais são enviados ao Firebase
4. Console mostra: ✅ Salvo: items/item-...
5. Sincronização completa!
```

**Como funciona**:
```javascript
// Sempre salva localmente primeiro
localStorage.setItem("renovaLotes:items", JSON.stringify(items));

// Tenta salvar no Firebase (se online)
if (firebaseInitialized) {
  saveToFirestore("items", item.id, item);
}
```

---

### 7. Recuperando Itens Excluídos

**Cenário**: Você excluiu um produto por engano

**Passo a passo**:
```
1. Vá para a aba "Lixeira"
2. Encontre o item excluído
3. Clique em "Restaurar"
4. Item volta para o estoque!
```

**Firebase**:
```javascript
// Antes (na lixeira):
lixeira/trash = {
  items: [
    {
      deletedAt: "2026-02-27T14:30:00Z",
      item: { id: "item-123", produto: "..." }
    }
  ]
}

// Depois (restaurado):
items/item-123 = { id: "item-123", produto: "..." }

// E removido da lixeira:
lixeira/trash = { items: [] }
```

---

### 8. Exportando e Importando Dados

#### Exportar para Backup:

**JSON (completo)**:
```
1. Clique em "Exportar JSON"
2. Arquivo salvo: renovaLotes-backup-2026-02-27.json
3. Contém: items + config + trash
4. Tamanho: ~500 KB (100 produtos)
```

**CSV (planilha)**:
```
1. Clique em "Exportar CSV"
2. Arquivo salvo: renovaLotes-export-2026-02-27.csv
3. Contém: apenas items
4. Pode abrir no Excel/Google Sheets
```

#### Importar de Backup:

```
1. Clique em "Importar"
2. Selecione o arquivo .json
3. Confirme a importação
4. Dados são mesclados (não substitui tudo)
5. Firebase recebe todas as alterações
```

**⚠️ ATENÇÃO**:
- Importar **NÃO apaga** dados existentes
- IDs duplicados são substituídos
- Faça backup antes de importar

---

### 9. Configurando Regras de Segurança

**Cenário**: Colocar em produção de forma segura

**Passo 1: Ativar Authentication**:
```
1. Firebase Console → Authentication
2. Clique em "Começar"
3. Ative "E-mail/Senha"
4. Crie um usuário de teste
```

**Passo 2: Atualizar Regras**:
```javascript
// Firebase Console → Firestore → Regras
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // Apenas usuários autenticados
    match /{document=**} {
      allow read, write: if request.auth != null;
    }
  }
}
```

**Passo 3: Adicionar Login no HTML** (opcional):
```html
<!-- Adicione antes do formulário -->
<div id="authSection">
  <input type="email" id="authEmail" placeholder="Email">
  <input type="password" id="authPassword" placeholder="Senha">
  <button onclick="login()">Entrar</button>
</div>

<script>
function login() {
  const email = document.getElementById('authEmail').value;
  const password = document.getElementById('authPassword').value;
  
  firebase.auth().signInWithEmailAndPassword(email, password)
    .then(user => {
      console.log("✅ Login bem-sucedido!");
      document.getElementById('authSection').style.display = 'none';
    })
    .catch(error => {
      alert("❌ Erro: " + error.message);
    });
}
</script>
```

---

### 10. Monitorando Uso do Firebase

**Onde ver**:
```
Firebase Console → Usage and billing
```

**O que monitorar**:
```
📊 Leituras de documentos
   - Cada vez que você abre a página: ~30 leituras
   - Criar item: 1 escrita
   - Editar item: 1 escrita
   - Excluir item: 1 escrita + 1 leitura

💾 Armazenamento
   - 100 produtos sem fotos: ~100 KB
   - 100 produtos com 3 fotos: ~45 MB
   - Limite free tier: 1 GB

📡 Transferência
   - Carregar 100 produtos: ~500 KB
   - Sincronizar 10 dispositivos/dia: ~5 MB/dia
   - Limite free tier: 10 GB/mês
```

**Otimizações**:
```javascript
// ✅ BOM: Carregar apenas o necessário
db.collection("items").where("vendido", "==", false).get();

// ❌ RUIM: Carregar tudo sempre
db.collection("items").get();

// ✅ BOM: Cache de configurações
let cachedConfig = null;
if (!cachedConfig) {
  cachedConfig = await loadFromFirestore("configuracoes", "config");
}

// ❌ RUIM: Buscar config toda hora
await loadFromFirestore("configuracoes", "config");
```

---

## 🎓 Dicas Avançadas

### 1. Pesquisa Otimizada:
```javascript
// Criar índice composto no Firestore Console:
// Collection: items
// Fields: vendido (ASC), dataVenda (DESC)

// Query rápida:
db.collection("items")
  .where("vendido", "==", true)
  .orderBy("dataVenda", "desc")
  .limit(20)
  .get();
```

### 2. Paginação:
```javascript
let lastVisible = null;

async function loadMore() {
  let query = db.collection("items").limit(20);
  
  if (lastVisible) {
    query = query.startAfter(lastVisible);
  }
  
  const snapshot = await query.get();
  lastVisible = snapshot.docs[snapshot.docs.length - 1];
  
  snapshot.forEach(doc => {
    items.push(doc.data());
  });
}
```

### 3. Batch Write (múltiplas escritas):
```javascript
const batch = db.batch();

items.forEach(item => {
  const ref = db.collection("items").doc(item.id);
  batch.set(ref, item);
});

await batch.commit();
// ✅ Salva até 500 itens em uma única operação!
```

---

## 🆘 Troubleshooting

### Problema: "Erro ao salvar"
```
Console mostra: ❌ Erro ao salvar items/item-123: 
  FirebaseError: Missing or insufficient permissions

Solução:
1. Verifique as regras de segurança
2. Se usar auth, verifique se está logado
3. Verifique se o Firebase está inicializado
```

### Problema: Dados não sincronizam
```
Console mostra: ⚠️ Firebase não configurado

Solução:
1. Abra index3.html
2. Procure por FIREBASE_CONFIG
3. Substitua "SUA_API_KEY" pelas credenciais reais
4. Salve e recarregue
```

### Problema: Página lenta
```
Possíveis causas:
- Muitas fotos grandes (>1MB cada)
- Muitos itens (>500)
- Internet lenta

Soluções:
1. Comprima as fotos antes de salvar
2. Implemente paginação
3. Use cache de configurações
4. Reduza qualidade das imagens
```

---

**✅ Agora você é expert em usar o sistema com Firebase!**

Para dúvidas, consulte:
- `FIREBASE-SETUP.md` - Configuração completa
- `ESTRUTURA-DADOS.md` - Detalhes técnicos
- `CONFIGURACAO-RAPIDA.md` - Setup em 5 minutos
