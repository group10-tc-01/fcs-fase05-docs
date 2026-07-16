# Conexão Solidária

Plataforma para conectar uma ONG a doadores por meio de campanhas de arrecadação e doações acompanhadas com transparência.

## Language

**GestorONG**:
Pessoa responsável por administrar campanhas e acompanhar a operação da ONG na plataforma.
_Avoid_: Admin, administrador

**Doador**:
Pessoa que se cadastra na plataforma para realizar doações a campanhas ativas.
_Avoid_: User, usuário

**Campanha**:
Iniciativa de arrecadação criada pela ONG com uma meta financeira e um período de vigência.
_Avoid_: Projeto, vaquinha

**Campanha Ativa**:
**Campanha** disponível publicamente para transparência e apta a receber **Intenção de Doação**.
_Avoid_: Campanha aberta, campanha publicada

**Doação**:
Contribuição financeira realizada por um **Doador** para uma **Campanha** ativa.
_Avoid_: Pagamento, transação

**Intenção de Doação**:
Pedido inicial de contribuição enviado pelo **Doador** antes do processamento assíncrono da **Doação**.
_Avoid_: Doação processada, pagamento confirmado

**Painel de Transparência**:
Visão pública das campanhas ativas e dos valores arrecadados processados.
_Avoid_: Dashboard administrativo, relatório interno

## Relationships

- Um **GestorONG** administra zero ou mais campanhas.
- Um **Doador** realiza zero ou mais doações.
- Uma **Campanha** recebe zero ou mais **Doações**.
- Uma **Campanha Ativa** pode receber zero ou mais **Intenções de Doação**.
- Uma **Intenção de Doação** pode resultar em uma **Doação** processada.
- O **Painel de Transparência** exibe apenas **Campanhas** ativas.
- O acesso administrativo pertence ao **GestorONG**.
- O envio de **Intenção de Doação** pertence ao **Doador**.
- O **GestorONG** pode consultar doações para fins de acompanhamento administrativo.

## Example dialogue

> **Dev:** "Um **Doador** pode criar campanhas?"
> **Domain expert:** "Não. Apenas um **GestorONG** pode administrar campanhas; o **Doador** participa realizando doações."

## Flagged ambiguities

- "Admin" e "User" vieram da fase 04, mas na Conexão Solidária os perfis canônicos são **GestorONG** e **Doador**.
- "Manager", "SuperAdmin" e permissões granulares não fazem parte do vocabulário do MVP; os perfis canônicos são **GestorONG** e **Doador**.
- "Pagamento" descreve infraestrutura financeira; no domínio da Conexão Solidária o termo canônico é **Doação**.
