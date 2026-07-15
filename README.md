# O Pastel Gigante - PDV

Sistema de PDV (Ponto de Venda) gratuito para pastelaria, rodando 100% no navegador via GitHub Pages. Sem servidor e sem custo de hospedagem.

Acesse: https://spyfer2-ux.github.io/pdv-pastelaria/

## Funcionalidades

- Atendimento por mesa (8 mesas). O cliente senta, pede e vai comendo; o pagamento fica para o final.
- Botao Cancelar Pedido: cancela o pedido caso o cliente desista, sem gerar venda.
- Botao Deixar em Aberto (pagar depois): registra os itens na mesa e volta para as mesas, sem cobrar.
- Cardapio completo de pasteis salgados, incluindo as categorias Atum e Berinjela.
- Adicionais em todos os pasteis salgados (23 opcoes: mussarela, catupiry, bacon, carne, escarola, palmito, etc.).
- Integracao com o app do Franqueado (CANDEIA) via Firebase.
- Caixa obrigatorio (abertura e fechamento).

## Caixa obrigatorio (abertura e fechamento)

O sistema controla o caixa por dia:

- **Abertura:** ao abrir o sistema, aparece a tela ABERTURA DE CAIXA com a data do dia e a pergunta "Quanto tem de troco no caixa?". E obrigatorio digitar o valor do troco para comecar a usar.
- **Durante o dia:** se o caixa ja foi aberto hoje, o sistema abre normal, sem pedir de novo.
- **Fechamento:** quando abre em um novo dia com o caixa do dia anterior ainda aberto, aparece a tela FECHAMENTO DE CAIXA, mostrando o faturamento do dia e a conferencia da gaveta (troco inicial + vendas em dinheiro = valor esperado). Ha um campo para digitar quanto tem no caixa e ver a diferenca.
- Depois de fechar, o sistema reinicia e pede a abertura do novo dia.

Os dados do caixa ficam salvos no proprio navegador (localStorage), na chave `opg_caixa`.

## Cobrancas e vencimentos

Na tela Gerencial (Admin) ha o botao **Cobrancas em Aberto**, que lista todas as cobrancas:

- Cobrancas recorrentes fixas: Mensalidade do Sistema (R$ 119,00 - todo dia 15), Aluguel do Estabelecimento (R$ 1.200,00 - todo dia 24) e Royalties da Marca (R$ 1.874,00 - todo dia 15, com isencao ate 07/08/2026).
- Boletos avulsos em aberto (status `pendente`) cadastrados no app CANDEIA, lidos em tempo real da colecao `boletos_avulsos` do Firebase.

A lista mostra cada cobranca ordenada pela proximidade do vencimento, com destaque para "em atraso", "vence hoje" e "vence amanha".

### Pop-up de vencimento (1 dia antes)

Ao abrir o PDV, um pop-up automatico avisa sobre qualquer cobranca (recorrente ou avulsa) que vence em exatamente 1 dia. O pop-up so aparece uma vez por dia (controle via localStorage, chave `pdv_venc_popup_visto`).

## Sincronizacao do historico de vendas (Atualizar Sistema)

As vendas sao gravadas localmente no navegador do aparelho (localStorage, chave `opg_vendas`) e tambem enviadas para a colecao `pdv_vendas` do Firebase, onde alimentam a aba **Faturamento** do app CANDEIA.

Para enviar o historico local (por exemplo, vendas antigas que ainda nao subiram para a nuvem):

- Ao abrir o PDV no aparelho onde estao as vendas, aparece o pop-up **"Atualizacao disponivel"** com o botao **Atualizar agora**. Ele sincroniza todas as vendas locais com a nuvem, mostrando barra de progresso.
- A qualquer momento, na tela Gerencial, o botao **Atualizar Sistema** dispara essa sincronizacao manualmente.

> Importante: o historico fica no aparelho onde o PDV roda. A sincronizacao precisa ser feita nesse mesmo aparelho.

## Tecnologia

- HTML, CSS e JavaScript em um unico arquivo (`index.html`).
- Firebase / Firestore (projeto `candeia-jr`) para sincronizar as vendas com o app do Franqueado (CANDEIA).
- Hospedagem gratuita no GitHub Pages.

### Colecoes do Firebase usadas

- `pdv_vendas`: vendas do PDV (campos: `timestamp`, `data` em ISO, `estab`, `total`, `itens`, `pagamentos`).
- `boletos_avulsos`: cobrancas avulsas cadastradas no CANDEIA (lidas para o pop-up e a lista de cobrancas).

### Chaves de localStorage

- `opg_produtos`, `opg_mesas`, `opg_adicionais`, `opg_caixa`: estado operacional do PDV.
- `opg_vendas`: historico de vendas do aparelho.
- `opg_historico_migrado`: marca que o historico ja foi sincronizado.
- `pdv_venc_popup_visto`: controla o pop-up de vencimentos (1x por dia).

## Como usar

1. Abra o sistema no navegador (celular, tablet ou computador).
2. Faca a abertura de caixa informando o troco.
3. Selecione a mesa e lance os pedidos.
4. Use Deixar em Aberto para mesas que pagam no final, ou finalize a venda.
5. No dia seguinte, faca o fechamento e confira a gaveta.
