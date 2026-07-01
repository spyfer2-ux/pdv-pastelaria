# O Pastel Gigante - PDV

Sistema de PDV (Ponto de Venda) gratuito para pastelaria, rodando 100% no navegador via GitHub Pages. Sem servidor e sem custo de hospedagem.

Acesse: https://spyfer2-ux.github.io/pdv-pastelaria/

## Funcionalidades

- Atendimento por mesa (8 mesas). O cliente senta, pede e vai comendo; o pagamento fica para o final.
- Botao Cancelar Pedido: cancela o pedido caso o cliente desista, sem gerar venda.
- Botao Deixar em Aberto (pagar depois): registra os itens na mesa e volta para as mesas, sem cobrar. O pagamento fica para o final.
- Cardapio completo de pasteis salgados, incluindo as categorias Atum e Berinjela.
- Adicionais em todos os pasteis salgados (23 opcoes: mussarela, catupiry, bacon, carne, escarola, palmito, etc.).
- Integracao com o app do Franqueado (aba FATURAMENTO PDV) via Firebase.

## Caixa obrigatorio (abertura e fechamento)

O sistema controla o caixa por dia:

- Abertura: ao abrir o sistema, aparece a tela ABERTURA DE CAIXA com a data do dia e a pergunta "Quanto tem de troco no caixa?". Nao da para pular: e obrigatorio digitar o valor do troco para comecar a usar.
- Durante o dia: se o caixa ja foi aberto hoje, o sistema abre normal, sem pedir de novo.
- Fechamento: quando abre em um novo dia com o caixa do dia anterior ainda aberto, aparece a tela FECHAMENTO DE CAIXA, mostrando o faturamento do dia (total de vendas e valores por forma de pagamento) e a conferencia da gaveta (troco inicial + vendas em dinheiro = valor esperado). Ha um campo para digitar quanto tem no caixa e ver a diferenca.
- Depois de fechar, o sistema reinicia e pede a abertura do novo dia.

Os dados do caixa ficam salvos no proprio navegador (localStorage), na chave opg_caixa.

## Tecnologia

- HTML, CSS e JavaScript em um unico arquivo (index.html).
- Firebase / Firestore para sincronizar as vendas com o app do Franqueado.
- Hospedagem gratuita no GitHub Pages.

## Como usar

1. Abra o sistema no navegador (celular, tablet ou computador).
2. Faca a abertura de caixa informando o troco.
3. Selecione a mesa e lance os pedidos.
4. Use Deixar em Aberto para mesas que pagam no final, ou finalize a venda.
5. No dia seguinte, faca o fechamento e confira a gaveta.
