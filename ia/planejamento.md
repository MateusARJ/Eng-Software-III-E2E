# Planejamento de Testes E2E com IA Generativa — Crypto Alert

Este documento registra o processo de utilização de Inteligência Artificial Generativa como ferramenta de apoio no levantamento de cenários e elaboração de rascunhos de fluxos End-to-End (E2E) com **Maestro** para o aplicativo **React Native Crypto Alert**, inspirado na metodologia proposta no artigo *GenIA-E2ETest: A Generative AI-Based Approach for End-to-End Test Automation* ([arXiv:2510.01024](https://arxiv.org/abs/2510.01024)).

---

## 1. Prompt Utilizado

O prompt abaixo foi elaborado descrevendo o domínio funcional do aplicativo em linguagem natural (sem fornecer o código-fonte completo), simulando o cenário real em que o testador parte dos requisitos funcionais e *User Stories* para solicitar à IA uma proposta de cenários e scripts iniciais do Maestro:

```text
Você é um engenheiro de QA especialista em testes mobile End-to-End (E2E) utilizando o framework Maestro em aplicações React Native / Expo.

Preciso planejar e criar a suíte de testes E2E para o aplicativo "Crypto Alert" (appId: com.anonymous.cryptoalert). O aplicativo possui a interface em inglês e conta com 3 abas principais na barra de navegação inferior:
1. "List": exibe a lista de criptomoedas (como Bitcoin, Ethereum, Cardano, Solana) com nome, símbolo, preço atual em USD e variação de 24h. Quando uma criptomoeda possui alertas configurados, o card dela exibe um indicador de alertas que pode ser expandido.
2. "Convert": permite converter um valor numérico de uma criptomoeda (ex: Bitcoin, Ethereum) para uma moeda fiduciária (ex: USD, BRL, EUR, GBP, JPY), exibindo o valor convertido na tela.
3. "Alerts": lista os alertas de preço criados pelo usuário ou exibe um estado vazio ("No alerts yet") com botão para criar um novo alerta ("Create Alert" / "New Alert"). Na tela de criação de alerta ("Create Alert"), o usuário escolhe a criptomoeda em um seletor modal, digita o preço-alvo em USD, escolhe a condição ("Above target price" ou "Below target price") e salva o alerta ("Save Alert"). Na lista de alertas, cada alerta criado pode ser excluído.

Com base nessa descrição:
1. Proponha 6 cenários de teste E2E cobrindo os fluxos principais, casos de validação e estados da interface.
2. Elabore rascunhos iniciais de scripts YAML do Maestro para os principais cenários propostos.
```

---

## 2. Resposta Original da IA

Abaixo está a resposta original gerada pela IA a partir do prompt em linguagem natural:

````markdown
Aqui estão 6 cenários de teste E2E propostos para o aplicativo **Crypto Alert**, seguidos pelos rascunhos de fluxos YAML para o Maestro:

### Cenários Propostos pela IA

1. **Cenário 1 — Listagem Inicial e Navegação entre Abas:**
   - **Objetivo:** Verificar se o aplicativo carrega a lista de criptomoedas na tela inicial e permite navegar entre as abas `List`, `Convert` e `Alerts`.
   - **Resultado esperado:** Cada aba exibe seu título principal correspondente (`Cryptocurrencies`, `Convert Crypto`, `Price Alerts`).

2. **Cenário 2 — Conversão de Criptomoeda para Moeda Fiduciária (USD e BRL):**
   - **Objetivo:** Converter uma quantidade de Bitcoin e Ethereum para Dólar (USD) e Real (BRL).
   - **Resultado esperado:** Ao digitar a quantidade, selecionar a criptomoeda e a moeda fiduciária e acionar a conversão, o valor exato convertido (ex: `$67,999.50` para 1 BTC) é exibido.

3. **Cenário 3 — Criação de Alerta de Preço ("Above target price") e Reflexo na Listagem:**
   - **Objetivo:** Criar um alerta de preço para o Bitcoin acima de um valor-alvo e verificar se ele aparece na aba `Alerts` e no card do Bitcoin na aba `List`.
   - **Resultado esperado:** O alerta é listado em `Price Alerts` e o card do Bitcoin na aba `List` passa a exibir o badge `1 alert`.

4. **Cenário 4 — Exclusão de Alerta de Preço e Retorno ao Estado Vazio:**
   - **Objetivo:** Criar um alerta de preço, excluí-lo na lista de alertas e confirmar a remoção.
   - **Resultado esperado:** Após confirmar a exclusão, o alerta desaparece da lista e o estado vazio (`No alerts yet`) volta a ser exibido.

5. **Cenário 5 — Validação de Erros ao Salvar Alerta com Campos Vazios:**
   - **Objetivo:** Tentar salvar um alerta sem selecionar criptomoeda e sem preencher o preço-alvo.
   - **Resultado esperado:** Ao tocar em `Save Alert` com o formulário vazio, o aplicativo deve exibir as mensagens de erro `"Please select a cryptocurrency"` e `"Please enter a target price"`.

6. **Cenário 6 — Simulação de Falha de Rede na Listagem de Criptomoedas:**
   - **Objetivo:** Desabilitar a conexão de rede e verificar a mensagem de erro `"Failed to load"` e o botão `"Try Again"`.
   - **Resultado esperado:** O estado de erro aparece e permite tentar novamente.

---

### Rascunhos Originais de Scripts YAML (Gerados pela IA)

#### Rascunho 1: `01-navigation.yaml`
```yaml
appId: com.anonymous.cryptoalert
---
- launchApp:
    clearState: true
- assertVisible: "Cryptocurrencies"
- assertVisible: "Bitcoin"
- assertVisible: "Ethereum"
- tapOn: "Convert"
- assertVisible: "Convert Crypto"
- tapOn: "Alerts"
- assertVisible: "Price Alerts"
- assertVisible: "No alerts yet"
- tapOn: "List"
- assertVisible: "Cryptocurrencies"
```

#### Rascunho 2: `02-conversion.yaml`
```yaml
appId: com.anonymous.cryptoalert
---
- launchApp
- tapOn: "Convert"
- assertVisible: "Convert Crypto"
- tapOn:
    id: "amount-input"
- inputText: "1"
- tapOn: "Cryptocurrency"
- tapOn: "Ethereum"
- tapOn: "Fiat Currency"
- tapOn: "BRL"
- tapOn: "Convert"
- assertVisible: "R$19,713.28"
```

#### Rascunho 3: `03-create-alert.yaml`
```yaml
appId: com.anonymous.cryptoalert
---
- launchApp:
    clearState: true
- tapOn: "Alerts"
- tapOn: "Create Alert"
- tapOn: "Select cryptocurrency"
- tapOn: "Bitcoin"
- tapOn:
    id: "price-input"
- inputText: "75000"
- tapOn: "Above target price"
- tapOn: "Save Alert"
- assertVisible: "Bitcoin"
- assertVisible: "$75,000.00"
- tapOn: "List"
- tapOn: "1 alert"
- assertVisible: "$75,000.00"
```

#### Rascunho 4: `04-delete-alert.yaml`
```yaml
appId: com.anonymous.cryptoalert
---
- launchApp:
    clearState: true
- tapOn: "Alerts"
- tapOn: "New Alert"
- tapOn: "Select cryptocurrency"
- tapOn: "Solana"
- tapOn:
    id: "price-input"
- inputText: "120"
- tapOn: "Below target price"
- tapOn: "Save Alert"
- assertVisible: "Solana"
- tapOn: "Delete"
- tapOn: "Confirm"
- assertVisible: "No alerts yet"
```

#### Rascunho 5: `05-empty-alert-validation.yaml`
```yaml
appId: com.anonymous.cryptoalert
---
- launchApp:
    clearState: true
- tapOn: "Alerts"
- tapOn: "Create Alert"
- tapOn: "Save Alert"
- assertVisible: "Please select a cryptocurrency"
- assertVisible: "Please enter a target price"
```
````

---

## 3. Cenários Selecionados para Implementação

Após revisar o código-fonte do aplicativo (`src/screens/` e `src/components/`) e inspecionar o comportamento real no emulador Android, foram selecionados e refinados **4 fluxos E2E** que cobrem de ponta a ponta todas as funcionalidades do sistema:

| ID | Arquivo no Repositório | Fluxo E2E Selecionado | Objetivo Principal |
| :--- | :--- | :--- | :--- |
| **Fluxo 01** | `.maestro/01-list-and-navigation.yaml` | Listagem de Criptomoedas e Navegação entre Abas | Validar o carregamento da lista de criptomoedas (`Bitcoin`, `Ethereum`, símbolos e preços) e a transição entre as abas `List`, `Convert` e `Alerts`, verificando os elementos estruturais de cada tela. |
| **Fluxo 02** | `.maestro/02-crypto-conversion.yaml` | Conversão Reativa entre Criptomoedas e Moedas Fiduciárias | Validar o estado inicial (`Enter an amount`), o cálculo reativo ao digitar uma quantidade no `TextInput` e a troca dinâmica de ativos via modais (`Bitcoin` $\rightarrow$ `Ethereum` e `USD` $\rightarrow$ `BRL`). |
| **Fluxo 03** | `.maestro/03-create-alert-and-badge.yaml` | Criação de Alerta (`Above target price`) e Badge na Listagem | Validar o cadastro completo de um alerta para `Bitcoin` com meta `$75,000.00`, a persistência na aba `Alerts` e a exibição/expansão do badge `1 alert` no card do `Bitcoin` na aba `List`. |
| **Fluxo 04** | `.maestro/04-manage-and-delete-alerts.yaml` | Navegação de Retorno, Criação (`Below target price`) e Exclusão de Alerta | Validar o botão de retorno (`Go back`), a criação de um alerta de queda (`Below target price`) para `Solana` (`$120.00`) e sua exclusão via `Delete alert`, comprovando o retorno imediato ao `EmptyState` (`No alerts yet`). |

---

## 4. Análise Crítica: O que foi Aproveitado, Corrigido ou Descartado (e Por Quê)

Seguindo as conclusões do artigo **GenIA-E2ETest**, um script gerado por IA a partir de linguagem natural funciona como um excelente ponto de partida estrutural, mas exige validação humana rigorosa contra a árvore de elementos real da aplicação. Abaixo detalhamos cada decisão tomada na revisão humana:

### 4.1. O que foi aproveitado
- **Decomposição das jornadas de usuário:** A organização em quatro grandes jornadas (navegação/listagem, conversão cambial, criação de alerta com impacto na tela inicial e ciclo de exclusão de alerta) cobriu 100% das telas e do estado global (`AlertContext` + `AsyncStorage`).
- **Uso de `clearState: true` no `launchApp`:** Essencial nos fluxos de alertas para garantir que o `AsyncStorage` (chave `cryptoAlerts`) inicie vazio e que os testes sejam determinísticos e independentes entre si.
- **Verificação cruzada entre telas (Fluxo 03):** A ideia sugerida pela IA de criar o alerta na aba `Alerts` e voltar para a aba `List` para clicar no badge `1 alert` foi integralmente mantida por representar um verdadeiro teste de integração E2E entre o `AlertProvider` e o componente `CryptoCard`.

### 4.2. O que precisou ser corrigido (e por quê)

1. **Remoção de IDs inexistentes (`id: "amount-input"`, `id: "price-input"`):**
   - **Problema na resposta da IA:** A IA alucinou seletores de `testID` (`amount-input` e `price-input`) que não existem nos arquivos `ConversionScreen.tsx` e `CreateAlertScreen.tsx`.
   - **Correção realizada:** No código real, ambos os `TextInput` utilizam `placeholder="0.00"` e não possuem `testID`. Substituímos os seletores alucinados por `tapOn: "0.00"`, que localiza o campo diretamente pelo texto de placeholder exposto na árvore de acessibilidade.

2. **Remoção do clique no botão inexistente `"Convert"` na tela de conversão:**
   - **Problema na resposta da IA:** No Rascunho 2, a IA incluiu `- tapOn: "Convert"` para disparar o cálculo de conversão (o que na verdade clicaria no ícone da própria aba inferior `"Convert"`, pois não existe botão de submissão no formulário).
   - **Correção realizada:** Inspecionando `useConversionScreen.ts`, verificamos que o cálculo é **reativo** via `useEffect` assim que o estado `amount` muda. Portanto, removemos o botão inexistente e adicionamos `hideKeyboard` logo após `inputText` para visualizar imediatamente o resultado em `"Converted Amount"`.

3. **Ajuste na abertura dos seletores `SelectField` na tela de conversão:**
   - **Problema na resposta da IA:** A IA tentou abrir o modal de criptomoeda clicando no rótulo estático `"Cryptocurrency"` e o de moeda fiduciária clicando em `"Fiat Currency"`.
   - **Correção realizada:** Em `SelectField.tsx`, o rótulo (`<Text style={selectFieldStyles.label}>{label}</Text>`) fica fora do `<Pressable>`. O botão clicável exibe o `displayLabel` do valor atualmente selecionado (`"Bitcoin"` por padrão para criptomoeda e `"USD"` por padrão para moeda fiduciária). Corrigimos os comandos para tocar em `"Bitcoin"` (abrindo o modal e escolhendo `"Ethereum"`) e tocar em `"USD"` (abrindo o modal e escolhendo `"BRL"`).

4. **Substituição de valores monetários fixos na conversão por Expressões Regulares:**
   - **Problema na resposta da IA:** A IA verificou `assertVisible: "R$19,713.28"` (calculado sobre o preço estático de fallback do Ethereum de `$3,850.25`).
   - **Correção realizada:** Em execução real com internet e chave da API CoinGecko configurada, `useConversionScreen.ts` busca a cotação em tempo real da API (`fetchCryptocurrenciesFromApi()`). Validar um valor estático faria o teste quebrar sempre que o preço de mercado do Ethereum oscilasse. Por isso, corrigimos a asserção para validar o padrão monetário formatado (`"\\$[0-9,.]+"` para USD e `"R\\$[0-9,.]+"` para BRL) e a ausência de `"Enter an amount"`.

5. **Correção do seletor de exclusão de alerta e remoção de modal de confirmação alucinado:**
   - **Problema na resposta da IA:** No Rascunho 4, a IA usou `- tapOn: "Delete"` seguido de `- tapOn: "Confirm"`.
   - **Correção realizada:** Inspecionando `AlertCard.tsx` (linhas 45–52), o botão de lixeira não possui texto visível `"Delete"`, mas sim `accessibilityLabel="Delete alert"`, e a função `deleteAlert(id)` remove o alerta imediatamente sem exibir modal de confirmação. Corrigimos o fluxo para `- tapOn: "Delete alert"` e validamos o retorno imediato ao `EmptyState` (`"No alerts yet"`).

6. **Fechamento explícito do teclado virtual (`hideKeyboard`):**
   - **Problema na resposta da IA:** Nos rascunhos de criação de alerta, a IA digitava o preço com `inputText` e tentava tocar imediatamente em `"Below target price"` e `"Save Alert"`.
   - **Correção realizada:** No emulador Android (`Pixel_7`), o teclado numérico (`keyboardType="decimal-pad"`) aberto pelo `TextInput` sobrepõe a parte inferior do `ScrollView`. Adicionamos `- hideKeyboard` logo após cada `inputText` para garantir que os rádios de condição e o botão `"Save Alert"` estejam sempre visíveis e clicáveis.

### 4.3. O que foi descartado (e por quê)

1. **Cenário 5 (Clicar em `"Save Alert"` vazio para testar mensagens de erro de campo obrigatório):**
   - **Motivo do descarte:** Ao analisar `CreateAlertScreen.tsx` (linha 92) e `useCreateAlertScreen.ts` (linhas 53–58), constatamos que o componente `PrimaryButton` recebe a propriedade `disabled={!isFormValid}`, onde `isFormValid` exige `selectedCrypto` preenchido e `Number(targetPrice) > 0`. Além disso, `usePrimaryButton.ts` bloqueia a execução de `onPress` quando `disabled` é `true`. Consequentemente, é impossível disparar `handleSave` pela interface quando o formulário está vazio — as mensagens `"Please select a cryptocurrency"` nunca são exibidas via toque no botão desabilitado. Esse é um exemplo clássico em que a IA sugere um teste inválido por desconhecer a regra de bloqueio preventivo da UI.

2. **Cenário 6 (Simulação de falha de rede com botão `"Try Again"`):**
   - **Motivo do descarte:** O Maestro não manipula nativamente de forma determinística o bloqueio seletivo da API CoinGecko sem afetar a própria conexão ADB/servidor interno do agente no emulador, e o aplicativo possui `.env` configurado para operar online na suíte principal. Por isso, priorizamos a cobertura profunda das interações reais nas quatro jornadas selecionadas (incluindo o teste do botão de navegação `"Go back"` no Fluxo 04).
