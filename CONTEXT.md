# Conexao Solidaria

Plataforma para conectar uma ONG a doadores por meio de campanhas de arrecadacao e doacoes acompanhadas com transparencia.

## Language

**GestorONG**:
Pessoa responsavel por administrar campanhas e acompanhar a operacao da ONG na plataforma.
_Avoid_: Admin, administrador

**Doador**:
Pessoa que se cadastra na plataforma para realizar doacoes a campanhas ativas.
_Avoid_: User, usuario

**Campanha**:
Iniciativa de arrecadacao criada pela ONG com uma meta financeira e um periodo de vigencia.
_Avoid_: Projeto, vaquinha

**Campanha Ativa**:
**Campanha** disponivel publicamente para transparencia e apta a receber **Intencao de Doacao**.
_Avoid_: Campanha aberta, campanha publicada

**Doacao**:
Contribuicao financeira realizada por um **Doador** para uma **Campanha** ativa.
_Avoid_: Pagamento, transacao

**Intencao de Doacao**:
Pedido inicial de contribuicao enviado pelo **Doador** antes do processamento assincrono da **Doacao**.
_Avoid_: Doacao processada, pagamento confirmado

**Painel de Transparencia**:
Visao publica das campanhas ativas e dos valores arrecadados processados.
_Avoid_: Dashboard administrativo, relatorio interno

## Relationships

- Um **GestorONG** administra zero ou mais campanhas.
- Um **Doador** realiza zero ou mais doacoes.
- Uma **Campanha** recebe zero ou mais **Doacoes**.
- Uma **Campanha Ativa** pode receber zero ou mais **Intencoes de Doacao**.
- Uma **Intencao de Doacao** pode resultar em uma **Doacao** processada.
- O **Painel de Transparencia** exibe apenas **Campanhas** ativas.
- O acesso administrativo pertence ao **GestorONG**.
- O envio de **Intencao de Doacao** pertence ao **Doador**.
- O **GestorONG** pode consultar doacoes para fins de acompanhamento administrativo.

## Example dialogue

> **Dev:** "Um **Doador** pode criar campanhas?"
> **Domain expert:** "Nao. Apenas um **GestorONG** pode administrar campanhas; o **Doador** participa realizando doacoes."

## Flagged ambiguities

- "Admin" e "User" vieram da fase 04, mas na Conexao Solidaria os perfis canonicos sao **GestorONG** e **Doador**.
- "Manager", "SuperAdmin" e permissoes granulares nao fazem parte do vocabulario do MVP; os perfis canonicos sao **GestorONG** e **Doador**.
- "Pagamento" descreve infraestrutura financeira; no dominio da Conexao Solidaria o termo canonico e **Doacao**.
