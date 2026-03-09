# Locadora de Veículos - Banco de Dados SQL

Projeto acadêmico desenvolvido na graduação em Ciência de Dados.  
Implementação completa de um banco de dados relacional para gerenciamento de uma locadora de veículos, incluindo criação de tabelas, inserção de dados de exemplo e consultas analíticas.

## Objetivo
Modelar e manipular dados de uma locadora fictícia, abrangendo:
- Cadastro de clientes
- Registro de veículos e manutenções
- Controle de pagamentos e locações
- Relacionamentos entre entidades (clientes → locações → veículos → pagamentos)
- Consultas para relatórios de negócio (arrecadação, veículos mais alugados, pendências de pagamento)

## Tecnologias Utilizadas
- **SQL** (MySQL compatível – testado em MySQL Workbench ou similar)
- Banco de dados relacional com chaves primárias, estrangeiras e integridade referencial
- Dados de exemplo realistas (clientes de várias regiões do Brasil, veículos populares, manutenções recentes)

## Estrutura do Banco de Dados

### Tabelas Principais
- **Cliente** → Dados pessoais e contato
- **Veiculo** → Informações do veículo (modelo, placa, valor diária, estado: Disponível/Alugado/Manutenção)
- **Manutencao** → Histórico de revisões e custos por veículo
- **Pagamento** → Forma, valor, data e estado (Pago/Pendente)
- **Locacao** → Período de aluguel, vinculado a cliente e pagamento
- **LocacaoVeiculo** → Tabela de relacionamento N:N (uma locação pode ter múltiplos veículos)

Diagrama conceitual (ER):  
- Cliente 1:N Locacao  
- Locacao 1:1 Pagamento  
- Locacao N:N Veiculo (via LocacaoVeiculo)  
- Veiculo 1:N Manutencao

## Como Executar
1. Abra o MySQL Workbench (ou outro client SQL compatível).
2. Crie um novo schema/banco: `CREATE DATABASE LocadoraVeiculos;`
3. Use o banco: `USE LocadoraVeiculos;`
4. Copie e cole todo o script SQL deste repositório (ou execute seção por seção: CREATE TABLE → INSERTs → SELECTs).
5. Execute na ordem: criação das tabelas → inserções → consultas de teste.

Alternativa simples (sem servidor MySQL instalado):  
- Use SQLite online (ex: https://sqliteonline.com) ou DB Browser for SQLite.
- Ajuste ligeiramente os tipos (ex: DECIMAL → REAL) se necessário.

## Principais Consultas Demonstradas

### 1. Histórico de Manutenções

SELECT descricao, dataManutencao, custo
FROM Manutencao;


###2. Total Arrecadado (apenas pagamentos confirmados)
SELECT SUM(valorTotal) AS TotalArrecadado
FROM Pagamento
WHERE estado = 'Pago';

###3. Veículos mais alugados (ranking)

SELECT v.modelo, v.marca, COUNT(lv.idLocacao) AS QtdeLocacoes
FROM Veiculo v
JOIN LocacaoVeiculo lv ON v.idVeiculo = lv.idVeiculo
GROUP BY v.modelo, v.marca
ORDER BY QtdeLocacoes DESC;

###4. Clientes com pagamentos pendentes

SELECT c.nome, SUM(p.valorTotal) AS ValorDevido
FROM Cliente c
JOIN Locacao l ON c.idCliente = l.idCliente
JOIN Pagamento p ON l.idPagamento = p.idPagamento
WHERE p.estado = 'Pendente'
GROUP BY c.nome
ORDER BY c.nome ASC;


