# 📊 Estrutura de Dados - Firebase Firestore

## 🗂️ Visão Geral das Collections

```
firestore (banco de dados)
│
├── 📁 configuracoes/
│   └── 📄 config
│       ├── canaisAnuncio: ["Shopee", "Mercado Livre", ...]
│       ├── canaisVenda: ["Shopee", "Instagram", ...]
│       ├── entregas: ["Correios", "Motoboy", ...]
│       ├── tiposProduto: ["Eletrônico", "Roupa", ...]
│       ├── locaisEstoque: ["Estoque Principal", ...]
│       ├── vendedores: ["João", "Maria", ...]
│       └── lots: [
│           {
│             id: "lote-1",
│             nome: "Lote 1",
│             total: 6050.00,
│             criadoEm: "2026-01-15"
│           }
│         ]
│
├── 📁 items/
│   ├── 📄 item-1708534567890
│   │   ├── id: "item-1708534567890"
│   │   ├── codigo: "COD001"
│   │   ├── produto: "Smartphone Samsung A54"
│   │   ├── categoria: "Eletrônico"
│   │   ├── loteId: "lote-1"
│   │   ├── custoProduto: 303.50
│   │   ├── vendido: true
│   │   ├── valorAnuncio: 850.00
│   │   ├── valorVenda: 800.00
│   │   ├── custoEntrega: 20.00
│   │   ├── localEstoque: "Estoque Principal"
│   │   ├── dataAnuncio: "2026-02-01"
│   │   ├── dataVenda: "2026-02-15"
│   │   ├── canaisAnuncio: ["Shopee", "Instagram"]
│   │   ├── canalVenda: "Shopee"
│   │   ├── entrega: "Correios"
│   │   ├── vendedor: "João"
│   │   ├── observacoes: "Cliente pediu nota fiscal"
│   │   ├── pagamento: {
│   │   │     tipo: "parcelado",
│   │   │     parcelas: [
│   │   │       { data: "2026-02-15", valor: 400.00 },
│   │   │       { data: "2026-03-15", valor: 400.00 }
│   │   │     ]
│   │   │   }
│   │   └── photos: ["data:image/jpeg;base64,...", "", ""]
│   │
│   ├── 📄 item-1708534567891
│   ├── 📄 item-1708534567892
│   └── 📄 ... (mais documentos)
│
└── 📁 lixeira/
    └── 📄 trash
        └── items: [
              {
                deletedAt: "2026-02-20T14:30:00.000Z",
                item: { /* estrutura completa do item */ }
              },
              { ... }
            ]
```

---

## 📋 Detalhamento dos Campos

### 🔧 Collection: `configuracoes`

| Campo | Tipo | Descrição | Exemplo |
|-------|------|-----------|---------|
| `canaisAnuncio` | Array\<string\> | Canais disponíveis para anúncios | `["Shopee", "Mercado Livre"]` |
| `canaisVenda` | Array\<string\> | Canais disponíveis para vendas | `["Instagram", "Venda Direta"]` |
| `entregas` | Array\<string\> | Métodos de entrega | `["Correios", "Motoboy"]` |
| `tiposProduto` | Array\<string\> | Categorias de produtos | `["Eletrônico", "Roupa"]` |
| `locaisEstoque` | Array\<string\> | Locais de armazenamento | `["Estoque Principal"]` |
| `vendedores` | Array\<string\> | Vendedores cadastrados | `["João", "Maria"]` |
| `lots` | Array\<Lot\> | Lotes de compra | Ver estrutura abaixo |

#### Estrutura de `Lot`:
```typescript
{
  id: string;           // Identificador único (ex: "lote-1")
  nome: string;         // Nome do lote (ex: "Lote Janeiro 2026")
  total: number;        // Valor total do lote (ex: 6050.00)
  criadoEm: string;     // Data de criação ISO (ex: "2026-01-15")
}
```

---

### 📦 Collection: `items`

| Campo | Tipo | Obrigatório | Descrição | Validação |
|-------|------|-------------|-----------|-----------|
| `id` | string | ✅ | ID único do item | Gerado automaticamente |
| `codigo` | string | ✅ | Código do produto | Min 1 caractere |
| `produto` | string | ✅ | Nome/descrição | Min 1 caractere |
| `categoria` | string | ✅ | Tipo de produto | Deve estar em `tiposProduto` |
| `loteId` | string | ✅ | ID do lote | Deve existir em `lots` |
| `custoProduto` | number | ✅ | Custo rateado | Calculado automaticamente |
| `vendido` | boolean | ✅ | Status de venda | `true` ou `false` |
| `valorAnuncio` | number | ⚠️ | Valor anunciado | Se anunciado, deve ser > 0 |
| `valorVenda` | number | ⚠️ | Valor vendido | Se vendido, obrigatório > 0 |
| `custoEntrega` | number | ❌ | Custo de envio | Padrão: 0 |
| `localEstoque` | string | ✅ | Local de armazenamento | Deve estar em `locaisEstoque` |
| `dataAnuncio` | string | ❌ | Data do anúncio | Formato: "YYYY-MM-DD" |
| `dataVenda` | string | ⚠️ | Data da venda | Se vendido, obrigatória |
| `canaisAnuncio` | Array\<string\> | ❌ | Canais de anúncio | Multi-seleção |
| `canalVenda` | string | ⚠️ | Canal de venda | Se vendido, obrigatório |
| `entrega` | string | ⚠️ | Método de entrega | Se vendido, obrigatório |
| `vendedor` | string | ⚠️ | Vendedor responsável | Se vendido, obrigatório |
| `observacoes` | string | ❌ | Notas adicionais | Texto livre |
| `pagamento` | Payment | ⚠️ | Info de pagamento | Se vendido, obrigatório |
| `photos` | Array\<string\> | ❌ | URLs ou base64 | Max 3 fotos |

#### Estrutura de `Payment`:
```typescript
{
  tipo: "avista" | "parcelado" | "outro";  // Tipo de pagamento
  parcelas: Array<{                        // Se parcelado
    data: string;                          // Data da parcela (ISO)
    valor: number;                         // Valor da parcela
  }>;
}
```

**Legenda**:
- ✅ = Sempre obrigatório
- ⚠️ = Obrigatório se item vendido
- ❌ = Opcional

---

### 🗑️ Collection: `lixeira`

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `items` | Array\<TrashItem\> | Lista de itens excluídos |

#### Estrutura de `TrashItem`:
```typescript
{
  deletedAt: string;    // Timestamp da exclusão (ISO 8601)
  item: Item;           // Objeto completo do item (estrutura de items)
}
```

**Regra de Limpeza**:
- Itens com `deletedAt` > 30 dias são automaticamente removidos
- Limpeza ocorre a cada inicialização do sistema

---

## 🔄 Fluxo de Dados

### Criação de Item:
```
1. Usuário preenche formulário
2. Sistema valida dados
3. Gera ID único: "item-" + timestamp
4. Salva em localStorage (backup)
5. Cria documento em items/{id}
6. Atualiza interface
```

### Atualização de Item:
```
1. Usuário edita item
2. Sistema valida mudanças
3. Atualiza localStorage
4. Atualiza documento em items/{id} (merge)
5. Recalcula KPIs se necessário
```

### Exclusão de Item:
```
1. Usuário clica em "Excluir"
2. Item é removido de items/{id}
3. Item é adicionado a lixeira/trash
4. Campo deletedAt é setado
5. Interface é atualizada
```

### Restauração de Item:
```
1. Usuário clica em "Restaurar"
2. Item é removido da lixeira
3. Item é re-criado em items/{id}
4. Interface é atualizada
```

---

## 💾 Tamanhos e Limites

### Firestore (Free Tier):
- **Documentos**: 50.000 leituras/dia
- **Escrita**: 20.000 escritas/dia
- **Armazenamento**: 1 GB
- **Transferência**: 10 GB/mês

### Limites Técnicos:
- **Documento**: Max 1 MB
- **Nome de campo**: Max 1.500 bytes
- **Profundidade**: Max 20 níveis aninhados
- **Array**: Max 20.000 elementos

### Recomendações:
- ✅ Fotos pequenas: Use compressão (max 800x800px)
- ✅ Base64 otimizado: Reduz até 70% do tamanho
- ⚠️ Fotos grandes: Considere Firebase Storage
- ⚠️ Muitos itens: Implemente paginação (>1000 itens)

---

## 🔐 Segurança dos Dados

### Dados Sensíveis (NÃO armazene):
- ❌ Senhas sem hash
- ❌ Números de cartão de crédito
- ❌ CPF/RG sem criptografia
- ❌ Chaves de API

### Dados Seguros (pode armazenar):
- ✅ Nomes de produtos
- ✅ Valores de venda
- ✅ Códigos de produtos
- ✅ Fotos de produtos (base64)
- ✅ Observações gerais

### Boas Práticas:
1. Configure regras de segurança adequadas
2. Não exponha credenciais Firebase publicamente
3. Use Firebase Authentication para dados privados
4. Implemente validação server-side (Cloud Functions)
5. Monitore uso no Firebase Console

---

## 📈 Escalabilidade

### Para até 100 itens:
✅ Estrutura atual funciona perfeitamente

### Para 100-1.000 itens:
⚠️ Considere:
- Paginação na listagem
- Índices no Firestore
- Cache de configurações

### Para mais de 1.000 itens:
⚠️ Necessário:
- Paginação obrigatória
- Índices compostos
- Cloud Functions para cálculos pesados
- Firebase Storage para fotos
- Otimização de queries

---

## 🛠️ Manutenção

### Backup Manual:
```javascript
// Exportar para JSON
1. Clique em "Exportar JSON"
2. Salve o arquivo em local seguro
3. Faça backup semanal
```

### Limpeza Automática:
- **Lixeira**: Limpa itens > 30 dias automaticamente
- **localStorage**: Sincroniza com Firebase
- **Cache**: Limpo a cada reload

### Monitoramento:
```
Firebase Console → Analytics
- Número de leituras/escritas por dia
- Uso de armazenamento
- Erros de segurança
```

---

**✅ Estrutura de dados segura, escalável e otimizada!**
