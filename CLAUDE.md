# GoRunking - Protótipo de Vendas de Ingressos

## Sobre o Projeto

Protótipo frontend de sistema de inscrição e venda de ingressos para o evento **40ª 10K Tribuna FM 2026** (corrida de rua em Santos/SP). Permite inscrições individuais e em grupo, com seleção de kit, modalidade e pagamento simulado.

## Stack

- HTML5 + CSS3 + JavaScript vanilla (ES6+) em arquivo único (`index.html`, ~4.700 linhas)
- Sem framework, sem build, sem backend
- Dependências externas via CDN: Google Fonts (Inter, Space Grotesk) e Font Awesome 6.5.1

## Como Rodar

```bash
# Abrir direto no navegador
open index.html

# Ou servir com HTTP server
python3 -m http.server 8000
```

Não precisa de build, install ou compilação.

## Estrutura

Arquivo único `index.html` contendo:
- **CSS** (linhas ~10-1850): Design system com variáveis CSS, mobile-first
- **HTML** (linhas ~1850-3100): Telas navegáveis via `goToScreen()`
- **JavaScript** (linhas ~3100+): Estado, navegação, formulários, importação, dados fake

## Telas/Fluxo

### Individual: Landing → Tipo → Kit → Dados do Atleta → Pagamento → Sucesso
### Grupo: Landing → Tipo (responsável/pagador) → Cadastro dos Atletas → Pagamento → Sucesso

1. **Landing** - Info do evento e modalidades
2. **Tipo** - Individual ou Grupo (mín. 10 atletas). Grupo coleta dados do responsável/pagador (CNPJ/CPF para NF, nome, telefone, email, senha)
3. **Kit** (individual only) - Modalidade, Kit (Básico R$149, Premium R$199, VIP R$299), Categoria, Camiseta
4. **Dados do Atleta** (individual) / **Revisão do grupo** (grupo)
5. **Cadastro dos Atletas** (grupo only) - 3 abas:
   - **Preencher manual**: card por card, cada atleta com modalidade, kit, categoria, camiseta
   - **Importar planilha**: upload de .xlsx com dados pessoais + review com bulk apply (modalidade, kit, categoria, camiseta para todos) e ajuste individual por atleta antes de confirmar
   - **Enviar link**: link compartilhável (copiar, WhatsApp, e-mail) para atletas preencherem seus dados
6. **Pagamento** - Grupo: PIX ou Boleto D+2 (dados do pagador = responsável, sem formulário extra). Individual: PIX ou Cartão à vista
7. **Sucesso** - Confirmação com protocolo e lista detalhada de atletas (grupo)
8. **Link do Atleta** - Inscrição individual via link compartilhado

## Convenções

- Telas: `screen-{nome}` (ex: `screen-landing`, `screen-payment`)
- Campos grupo: `gf-{campo}-{indice}` / Campos link atleta: `al-{campo}`
- Campos importação: `imp-{campo}-{indice}` / Bulk: `bulk-{campo}`
- Estado global no objeto `state` (type, groupSize, modality, kit, payment, groupResponsible, etc.)
- Navegação por hash (`#grupo`, `#atleta`, `#sucesso`)
- Dados fake para teste com nomes brasileiros, CPFs e e-mails gerados

## Regras de Negócio (Grupos)

- **Fluxo**: cadastrar atletas ANTES do pagamento (não depois)
- **Responsável = pagador**: coleta CNPJ/CPF (para NF), nome, telefone, email e senha. Esses dados são usados como pagador — sem formulário de comprador na tela de pagamento
- **Duplicidade CNPJ/CPF**: trava inscrição de mais de um grupo por CPF/CNPJ
- **Kit/Modalidade/Categoria/Camiseta**: escolhidos POR ATLETA (não global do grupo)
- **Categorias com meia-entrada**: Amador 60+ e PCD pagam 50% do kit
- **Preço em tempo real**: resumo sticky atualiza conforme atletas são cadastrados
- **Prazo**: 5 dias para preencher os atletas. Cobrança gerada automaticamente ao finalizar ou ao expirar o prazo (com atletas cadastrados até o momento)
- **Importação de planilha**: upload mockup de .xlsx com review em tabela, bulk apply para modalidade/kit/categoria/camiseta + ajuste individual antes de confirmar
- **Link compartilhável**: cada atleta pode preencher seus próprios dados via link
- **Duplicidade de atletas**: barra CPF duplicado dentro do grupo
- **Validação nascimento**: idade mínima 18 anos
- **Sem multa**: permite finalizar grupo sem preencher todas as vagas (vagas remanejáveis)
- **Pagamento grupo**: apenas Boleto D+2 ou PIX
- **Pagamento individual**: apenas PIX ou Cartão sem parcelamento
- **Edição de atletas**: permite editar atletas já salvos a qualquer momento
- **Link VIP**: link exclusivo gerado quando algum atleta tem kit VIP

## Observações

- Protótipo apenas frontend — nenhum dado é enviado a servidor
- Pagamentos, importação de planilha e confirmações são simulados
- Preços: `kitPrices = { basic: 149, premium: 199, vip: 299 }` com taxa de 8%
- Timer de 14:59 na tela de seleção de kit (client-side)
