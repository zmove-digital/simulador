# Simulador de Financiamento Imobiliário (PRICE & SAC)

App web estático para embed no site da empresa de engenharia. Calcula parcelas pelos métodos PRICE e SAC, com taxa de juros puxada da API pública do BACEN (SGS).

## Como usar

1. Abra `index.html` no navegador ou incorpore via `<iframe src=".../index.html" ...>`. Veja `COMO_INSERIR.md` para os códigos prontos de incorporação (iframe com altura automática, link ou inline).
2. Informe valor do imóvel, entrada, prazo e taxa. Opcionalmente informe a renda bruta para checar o comprometimento de renda (padrão 30%). A taxa pode ser atualizada automaticamente pelo botão **Atualizar taxa do BACEN**.
3. Clique em **Calcular simulação**.

## Fonte de dados

- API pública do Banco Central (SGS): `https://api.bcb.gov.br/dados/serie/bcdata.sgs.{codigo}/dados`
- Série padrão: `25498` (taxa média mensal de juros para financiamento imobiliário, anualizada no app).
- Outras séries selecionáveis no app: `25497`, `20739`, `20745`.

## Comprometimento de renda

- Informe a **renda bruta mensal** e o **comprometimento máximo** (padrão 30%).
- Quando a 1ª parcela (PRICE ou SAC) superar o limite, o app mantém o valor do imóvel e **aumenta a entrada** até a parcela caber na renda (se não for possível, diminui o valor do imóvel). Exibe alerta, a **parcela máxima**, o **valor máximo financiável** e de imóvel, além de um botão para **restaurar os valores originais**.

- Valores são estimativos e sujeitos à análise de crédito.
- O app não depende de backend; funciona 100% no navegador.
- A chamada ao BACEN pode falhar por CORS/rede; nesse caso use a taxa manual.

## Estrutura

```
SIMULADOR_CEF/
└── index.html   # app completo (HTML+CSS+JS)
```
