# Integridades Referenciais e suas Restrições
Este artigo tem como objetivo esclarecer os conceitos e citar exemplos práticos
dos tipos de FOREIGN KEY para garantir uma integridade referencial de acordo com a sua regra de negócio. As formas abaixo são uteis no dia a dia de cada desenvolvedor/dba.

O oracle permite 3 tipos diferentes de integridades referenciais, sendo elas:
**ON DELETE CASCADE**, **ON DELETE SET NULL** E **ON DELETE NO ACTION** (PADRÃO).


Para exemplificar nosso caso iremos criar 4 tabelas (CLIENTE, PRODUTO, PEDIDO e ITEM_PEDIDO). 

```
/*
	PARA NOSSO TESTE CRIAMOS UM SCHEMA/USUARIO CHAMADO GPO COM A SENHA gpo12345
	Para reproduzir nosso script você cria-lo e abrir uma conexão através dele,
	ou então alterar para outro na criação das tabelas e inserts.	
*/

-- CRIAR USUÁRIO
CREATE USER GPO IDENTIFIED BY gpo12345;

-- DA PERMISSÃO PARA O USUÁRIO UTILIZAR A TABLESPACE SYSTEM
ALTER USER GPO QUOTA UNLIMITED ON SYSTEM;

/*--------------------------------------------------------
--DDL for Table CLIENTE
--------------------------------------------------------*/
CREATE TABLE GPO.CLIENTE
(
	ID_CLIENTE     	NUMBER NOT NULL
	, NO_CLIENTE   	VARCHAR(80) NOT NULL
	, NR_CPF		VARCHAR(11) NOT NULL
	, DT_NASCIMENTO DATE
	, CONSTRAINT PK_CLIENTE PRIMARY KEY (ID_CLIENTE)
);

INSERT INTO GPO.CLIENTE (ID_CLIENTE,NO_CLIENTE, NR_CPF, DT_NASCIMENTO) VALUES (1, 'JOÃO DA SILVA', '12345678910',TO_DATE('01/01/1990','dd/mm/yyyy'));
INSERT INTO GPO.CLIENTE (ID_CLIENTE,NO_CLIENTE, NR_CPF, DT_NASCIMENTO) VALUES (2, 'MARIA PEREIRA', '10987654321',TO_DATE('01/01/1970','dd/mm/yyyy'));
INSERT INTO GPO.CLIENTE (ID_CLIENTE,NO_CLIENTE, NR_CPF, DT_NASCIMENTO) VALUES (3, 'JOSÉ FEREIRA', '15987423687',TO_DATE('01/01/1980','dd/mm/yyyy'));

/*--------------------------------------------------------
--DDL for Table PRODUTOS
--------------------------------------------------------*/
CREATE TABLE GPO.PRODUTO
(
	ID_PRODUTO  NUMBER NOT NULL 
	, NO_PRODUTO VARCHAR(50) NOT NULL
	, DE_PRODUTO  VARCHAR(500) NOT NULL
	, VL_UNITARIO NUMBER(9,2)   NOT NULL
	, CONSTRAINT PK_PRODUTO PRIMARY KEY (ID_PRODUTO)
); 

INSERT INTO GPO.PRODUTO (ID_PRODUTO,NO_PRODUTO, DE_PRODUTO, VL_UNITARIO) VALUES (1,'CAIXA DE SOM','Caixa de som 5.1',200);
INSERT INTO GPO.PRODUTO (ID_PRODUTO,NO_PRODUTO, DE_PRODUTO, VL_UNITARIO) VALUES (2,'MONITOR','Monitor TV 24"',350);
INSERT INTO GPO.PRODUTO (ID_PRODUTO,NO_PRODUTO, DE_PRODUTO, VL_UNITARIO) VALUES (3,'SMARTPHONE IPHONE 11','Smartphone Iphone 11 vermelho....',4900);
INSERT INTO GPO.PRODUTO (ID_PRODUTO,NO_PRODUTO, DE_PRODUTO, VL_UNITARIO) VALUES (4,'IMPRESSORA EPSON','Impressora EPSON tanque de tinta',1100);
INSERT INTO GPO.PRODUTO (ID_PRODUTO,NO_PRODUTO, DE_PRODUTO, VL_UNITARIO) VALUES (5,'VIDEO GAME X-BOX','Video Game X-box com 2 controles',1200);
INSERT INTO GPO.PRODUTO (ID_PRODUTO,NO_PRODUTO, DE_PRODUTO, VL_UNITARIO) VALUES (6,'MOUSE SEM FIO','Mouse sem fio com botões de atalho',500);
INSERT INTO GPO.PRODUTO (ID_PRODUTO,NO_PRODUTO, DE_PRODUTO, VL_UNITARIO) VALUES (7,'TECLADO SEM FIO','Teclado sem fio ergonomico',200);
INSERT INTO GPO.PRODUTO (ID_PRODUTO,NO_PRODUTO, DE_PRODUTO, VL_UNITARIO) VALUES (8,'HEADSET COM FIO','Headset com fio e microfone',120);
INSERT INTO GPO.PRODUTO (ID_PRODUTO,NO_PRODUTO, DE_PRODUTO, VL_UNITARIO) VALUES (9,'HEADSET BLUETOOH','Headset Bluetooh audio 5.1',150);
INSERT INTO GPO.PRODUTO (ID_PRODUTO,NO_PRODUTO, DE_PRODUTO, VL_UNITARIO) VALUES (10,'MODEM ROTEADOR','Modem roteador 300Mbps',200);

/*--------------------------------------------------------
--DDL for Table PEDIDO
--------------------------------------------------------*/
CREATE TABLE GPO.PEDIDO
(
	ID_PEDIDO  NUMBER NOT NULL
	, ID_CLIENTE NUMBER
	, DT_PEDIDO  DATE NOT NULL
	, VL_TOTAL_PEDIDO NUMBER(9,2)
	, CONSTRAINT PK_PEDIDO PRIMARY KEY (ID_PEDIDO)
	, CONSTRAINT FK_PEDIDO_CLIENTE FOREIGN KEY (ID_CLIENTE) REFERENCES GPO.CLIENTE (ID_CLIENTE) ON DELETE SET NULL
); 

INSERT INTO GPO.PEDIDO (ID_PEDIDO,ID_CLIENTE, DT_PEDIDO, VL_TOTAL_PEDIDO) VALUES (1,1,sysdate-10,650);
INSERT INTO GPO.PEDIDO (ID_PEDIDO,ID_CLIENTE, DT_PEDIDO, VL_TOTAL_PEDIDO) VALUES (2,2,sysdate-20,5100);
INSERT INTO GPO.PEDIDO (ID_PEDIDO,ID_CLIENTE, DT_PEDIDO, VL_TOTAL_PEDIDO) VALUES (3,1,sysdate-30,970);

/*--------------------------------------------------------
--DDL for Table ITEM_PEDIDO
--------------------------------------------------------*/
CREATE TABLE GPO.ITEM_PEDIDO
(
	ID_ITEM_PEDIDO NUMBER NOT NULL
	, ID_PEDIDO  NUMBER NOT NULL
	, ID_PRODUTO  NUMBER NOT NULL
	, QT_ITEM  NUMBER NOT NULL
	, VL_TOTAL NUMBER(9,2)
	, CONSTRAINT PK_ITEM_PEDIDO PRIMARY KEY (ID_ITEM_PEDIDO)
	, CONSTRAINT FK_ITEM_PEDIDO_PEDIDO FOREIGN KEY (ID_PEDIDO) REFERENCES GPO.PEDIDO (ID_PEDIDO) ON DELETE CASCADE
    , CONSTRAINT FK_ITEM_PEDIDO_PRODUTO FOREIGN KEY (ID_PRODUTO) REFERENCES GPO.PRODUTO (ID_PRODUTO) ON DELETE NO ACTION
); 

INSERT INTO GPO.ITEM_PEDIDO (ID_ITEM_PEDIDO, ID_PEDIDO,ID_PRODUTO, QT_ITEM, VL_TOTAL) VALUES (1,1,1,1,200);
INSERT INTO GPO.ITEM_PEDIDO (ID_ITEM_PEDIDO, ID_PEDIDO,ID_PRODUTO, QT_ITEM, VL_TOTAL) VALUES (2,1,2,1,350);
INSERT INTO GPO.ITEM_PEDIDO (ID_ITEM_PEDIDO, ID_PEDIDO,ID_PRODUTO, QT_ITEM, VL_TOTAL) VALUES (3,1,3,1,4900);
INSERT INTO GPO.ITEM_PEDIDO (ID_ITEM_PEDIDO, ID_PEDIDO,ID_PRODUTO, QT_ITEM, VL_TOTAL) VALUES (4,1,4,1,1100);
INSERT INTO GPO.ITEM_PEDIDO (ID_ITEM_PEDIDO, ID_PEDIDO,ID_PRODUTO, QT_ITEM, VL_TOTAL) VALUES (5,2,5,1,1200);
INSERT INTO GPO.ITEM_PEDIDO (ID_ITEM_PEDIDO, ID_PEDIDO,ID_PRODUTO, QT_ITEM, VL_TOTAL) VALUES (6,2,1,1,200);
INSERT INTO GPO.ITEM_PEDIDO (ID_ITEM_PEDIDO, ID_PEDIDO,ID_PRODUTO, QT_ITEM, VL_TOTAL) VALUES (7,2,3,1,4900);
INSERT INTO GPO.ITEM_PEDIDO (ID_ITEM_PEDIDO, ID_PEDIDO,ID_PRODUTO, QT_ITEM, VL_TOTAL) VALUES (8,3,6,1,500);
INSERT INTO GPO.ITEM_PEDIDO (ID_ITEM_PEDIDO, ID_PEDIDO,ID_PRODUTO, QT_ITEM, VL_TOTAL) VALUES (9,3,7,1,200);
INSERT INTO GPO.ITEM_PEDIDO (ID_ITEM_PEDIDO, ID_PEDIDO,ID_PRODUTO, QT_ITEM, VL_TOTAL) VALUES (10,3,8,1,120);
INSERT INTO GPO.ITEM_PEDIDO (ID_ITEM_PEDIDO, ID_PEDIDO,ID_PRODUTO, QT_ITEM, VL_TOTAL) VALUES (11,3,9,1,150);
```
**ON DELETE CASCADE** é utilizada quando queremos remover o registro pai e seus filhos também, ou seja, os registros a qual ele é referenciado. Essa não é uma prática comum, mas pode ser utilizada em alguns casos. 
Esse caso seria útil caso quiséssemos excluir um pedido e seus itens vinculados de forma automática.

Nessa imagem mostramos todos os pedidos.
```
SELECT * 
FROM   GPO.PEDIDO;
```
![Image](https://bn1301files.storage.live.com/y4ml_5isf2xTxc0AJ9xuNuKA-YDBUfZ_heaaIVU5faOgrWEQvYbX5Mimi7pxQf7nUZLWK7E4dMXgHLHvq4IYHgWHHF__F8lbkY6ckbrqoWB7ZY6y4zMOHy10BVBKJGkr091ANSgEIn3JTL6xiV5PK1Xj0Nrqpwm0T37uISqjqZiqTX0g0FLEksn1R9waEvLCrDJ?width=1273&height=633&cropmode=none)


Nessa imagem mostramos os itens do pedido número 3.
```
SELECT * 
FROM  GPO.ITEM_PEDIDO
WHERE ID_PEDIDO = 3;
```
![Image](https://bn1301files.storage.live.com/y4mG9w2COpyOIt1t0CIoN8j0TE8cBg-Sb1fp-5YuFxa8-xlu5NOzzykRIvnEqZESAAdShJ90x8Dn7NUBwO0Ny5UUvmUjOv9Gm0gNjhivaMIaCu7gOD62BYJ29JPNk9hbkPF8dB50fv2wek7FwCVpTp8nrMfo-Fh9pYA7CdFhKapsPWuKZvHvZ8KUTEtkNs8HejJ?width=1275&height=633&cropmode=none)


Agora vamos deletar o Pedido, que com sua constraint definida como ON DELETE CASCADE automaticamente irá excluir os registros vinculados na ITEM_PEDIDO.
```
DELETE GPO.PEDIDO
WHERE ID_PEDIDO = 3;
```
![Image](https://bn1301files.storage.live.com/y4m0VvWkrCVlfRkLqY_68b8U_35Xm2tWj7a1pgdNcSY5fknHE8a_Q-2QD0CfBw4jDy6Uv-XXH-w4xj2djyrzCFZd13wWbfgWirFnZHkuMHduXP3SeWDZG0b3EkKeyEVpYGXgy-0eyKGycxLDPwpXcNpRcZaNbYQJTe_wNntIjiZgFkbG2ce0kGlelqPeBbc17-R?width=1277&height=643&cropmode=none)


Verificando a tabela ITEM_PEDIDO confirmamos que os 4 registros filhos foram deletados.
```
SELECT * 
FROM  GPO.ITEM_PEDIDO
WHERE ID_PEDIDO = 3;
```
![Image](https://bn1301files.storage.live.com/y4mYaiPBlSeOa0CBtHThueSzwCeZjXN1F7_i9D8Vi7ow1XCPBDd58rX-SpKOdbrZk6GCOJLMlL2vCQ0jR4Q_WZ-2zwpuOTXiA62I70lu_4rnE2BB64edUNX2zK5ZIBxdY8CuTlUC_u2iC82PabN4uLWvgLFJS-5cKoYpq9U1mneyXQL1IBLlphjzlOx4smJyuZN?width=1273&height=633&cropmode=none)

Atenção constraints do tipo ON DELETE CASCADE devem ser utilizadas somente em casos específicos onde a regra de negocio da aplicação exija esse comportamento, pois a mesma aumenta o risco de perdas de dados.




**ON {DELETE|UPDATE} SET NULL** é suportado tanto para comandos DELETE quanto UPDATE, nesse caso quando é feito o delete ou update do registro pai, o registro filho será atualizado para NULL. 
É importante lembrar que quando especificamos SET NULL não podemos declarar a coluna na tabela filha como NOT NULL. Mas em qual caso isso seria útil?

Com a LGPD(Lei Geral de Proteção de Dados) em vigor qualquer pessoa tem direito de solicitar para qualquer empresa que apague seus dados pessoais do sistema.

Em nosso caso o cliente de código 2-Maria Pereira solicitou para que seus dados sejam apagados, mas nós não queremos perder o histórico de pedidos da Maria para que isso não afete os relatórios de vendas. Então nesse caso o ON DELETE SET NULL seria ótimo para nós. Veja as imagens.

Nessa imagem mostramos a tabela de cliente.
![Image](https://bn1301files.storage.live.com/y4mONypyvbleny1k1UW0XwAjktWMK43gs98R9ZiYZWRDZeyRJkLH_U9L8wkfc5b19ZDgdnwen1yTSl22Unqf_VfpeL6sCcpTu15QMtOjrOt39xyyj6dLRpMdD_BVh1qImcmKvKCRi4xS1QuQ4s0o7dx9wBHe6jpOx3kF6eJn_7mBkFe8j8dTJrVudFH_bqNfeL7?width=1275&height=645&cropmode=none)


Nessa imagem mostramos todos os Pedidos, entre eles o pedido do cliente de código 2-Maria Pereira.
```
SELECT * 
FROM   GPO.PEDIDO;
```
![Image](https://bn1301files.storage.live.com/y4mgNy03qUIe-hq-I0ESIhreAWrSRz6svck2a2SmE7NH534Vm08pEP3JQsiDx4oxWWAy78HDxFozX5_sGD8adfWr_je1K8vfcPhJIEP4EBYHjBSWOkliMLBIWTWmywwmovfilxfyeFf8BhNlrfDTguq66YkdY9KV3EvPXaPoFcZB7amgpL69FPfGDJBWWRTg4Ww?width=1277&height=641&cropmode=none)


Agora deletamos o cliente. 
```
DELETE FROM GPO.CLIENTE WHERE ID_CLIENTE = 2;
```
![Image](https://bn1301files.storage.live.com/y4mh0qxpyswYZTLPUUEf-6G-nnnB8NXKRDjWtOePcbz44fasP-Jhz_R8eIGhrJNftDWvJD0Mbr18RjiavZliAoB_Ck4aUDsmybcgyqgm6I8Pt8EMrUvXaCnTuGIflVhzFFgCraqV4sEPjgAoqPv0UjoyxFpkCeIYKga-C9nFqHiZvpU_F5tUFg7JD9-SEBzDTvn?width=1283&height=643&cropmode=none)


Verificando como ficou o pedido.
```
SELECT * 
FROM   GPO.PEDIDO;
```
![Image](https://bn1301files.storage.live.com/y4moTRDwA4Hgk3eHtim5Tu3ztfpJik089hQj3y-C0TRnNrN96iS5RtjQHKo8AvxNjQ9VQfazDh-eYUyDCLKAFoHHCrRsm9y0lrUqEiKIwmT_Uwp9DCof-j7nkcEUEa5f7KoRtlfijVE1vcgMxHLVhIJvqH-ictDIMbgla9FcWE8BqBu8UvU3-LcEVdigCKjhixE?width=1277&height=641&cropmode=none)
Atenção constraints do tipo ON {DELETE|UPDATE} SET NULL devem ser utilizadas somente em casos específicos onde a regra de negocio da aplicação exija esse comportamento, pois a mesma aumenta o risco de perdas de dados.
ON {DELETE|UPDATE} NO ACTION é a restrição padrão quando não informamos nenhuma na criação da tabela com constraints ou em comandos Alter table add constraints. Se você tentar deletar um registro pai a qual tenha registros filhos referenciados ocorrerá um erro. Vamos a um exemplo.
Desejamos deletar um PRODUTO cadastrado em nossa base de dados, no entanto esse produto já foi vendido diversas vezes e por isso está referenciado na tabela ITEM_PEDIDO.

Verificando os produtos cadastrados.
```
SELECT * 
FROM   GPO.PRODUTO;
```
![Image](https://bn1301files.storage.live.com/y4mnIfyQwEKxXzyuSbht_xOguc16rYxFFonkNlUxdUbZDJVvR9BZLWi6eOveTEILgsI8j-NvmDhrdAv6aOoIrAiU_-rDG3amuecIfQuI8WlzxqYxQVJo3RqP-r27eIGm8AzbC5A8F3dFJJDAU-eteEG5aze7lvvcbccr4uyz61QyIl98vXLqz41b_Zydov5IGFz?width=1279&height=639&cropmode=none)


Verificando os produtos que estão na tabela ITEM_PEDIDO.
```
SELECT * 
FROM   GPO.ITEM_PEDIDO;
```
![Image](https://bn1301files.storage.live.com/y4mHgAFk9QhHTwvmIu44hF3xonLpj9RHrzM4dPUCZWN0GEEYXhnSi2KkvCTTHEmUZOJBHy0AekYgdIcSyA2i-_fa4cOYJjoE3sHOjAD5zk8W6WPey5pkvO83p0bWPUgggbLsKtaYU6ksgSglBrMlncVlCAVeoZAnYUSTO6Maix90yi7v8Moi1AXokrHLSP3WfQ_?width=1275&height=633&cropmode=none)


Notamos que existem dois registros para referenciando o ID_PRODUTO 1, vamos tentar apaga-lo e acontecera o erro ORA-02292 restrição de integridade violada, ou seja não podemos deletar o produto 1 enquanto houver registros referenciando ele em outras tabelas.
```
DELETE FROM GPO.PRODUTO
WHERE ID_PRODUTO = 1;
```
![Image](https://bn1301files.storage.live.com/y4m9Kyn5a-aZraDHkPgHGoMyiDbor8eI0raVbWqS5HJ05RYWvQE2JtwXuqtHbbQ5YGUX9gzrdPXBBzKlNR4d3VjCFKSURvSalLpHylCtES0tYwq03NtPfGi4mEMUwZqzA1PeBll7kx_bA61tCzb4-Se0uLxC9wZcW1S4sO5a9ttfMLov2sLMbnFxqesXXfWu6xv?width=1277&height=635&cropmode=none)


Uma observação importante é que não é permitido informar o ON DELETE NO ACTION no momento da criação da Foreign Key, basta não mencionar nenhuma outra restrição que ela será criada automaticamente. Caso tente criar a foreign key indicando ON DELETE NO ACTION acontecerá um erro de sintaxe.
```
CREATE TABLE GPO.ITEM_PEDIDO
(
	ID_ITEM_PEDIDO NUMBER NOT NULL
	, ID_PEDIDO  NUMBER NOT NULL
	, ID_PRODUTO  NUMBER NOT NULL
	, QT_ITEM  NUMBER NOT NULL
	, VL_TOTAL NUMBER(9,2)
	, CONSTRAINT PK_ITEM_PEDIDO PRIMARY KEY (ID_ITEM_PEDIDO)
	, CONSTRAINT FK_ITEM_PEDIDO_PEDIDO FOREIGN KEY (ID_PEDIDO) REFERENCES GPO.PEDIDO (ID_PEDIDO) ON DELETE CASCADE
    , CONSTRAINT FK_ITEM_PEDIDO_PRODUTO FOREIGN KEY (ID_PRODUTO) REFERENCES GPO.PRODUTO (ID_PRODUTO) ON DELETE NO ACTION
);
```
![Image](https://bn1301files.storage.live.com/y4m864xoDrykdvCixdhoe26hG9yDywr1YoFTFhCgzkOBchE_yf7AblNwfaUfxjtE23Ew5EgRWacvOpv2TaXtMMStgFJJWt7H8QJT4LjIdXaaDDRrUTUsyn5i7AfFyFl-6G6ro2ggbCYkVamrAvNNrigzx1nCvBZyDiqNjD1X99uvTb33CL82r-3gbRTWoraa9ny?width=1269&height=635&cropmode=none)



Para criar a foreign key como ON DELECT NO ACTION basta não mencionar nenhuma outra restrição que ela será criada automaticamente.
```
CREATE TABLE GPO.ITEM_PEDIDO
(
	ID_ITEM_PEDIDO NUMBER NOT NULL
	, ID_PEDIDO  NUMBER NOT NULL
	, ID_PRODUTO  NUMBER NOT NULL
	, QT_ITEM  NUMBER NOT NULL
	, VL_TOTAL NUMBER(9,2)
	, CONSTRAINT PK_ITEM_PEDIDO PRIMARY KEY (ID_ITEM_PEDIDO)
	, CONSTRAINT FK_ITEM_PEDIDO_PEDIDO FOREIGN KEY (ID_PEDIDO) REFERENCES GPO.PEDIDO (ID_PEDIDO) ON DELETE CASCADE
    , CONSTRAINT FK_ITEM_PEDIDO_PRODUTO FOREIGN KEY (ID_PRODUTO) REFERENCES GPO.PRODUTO (ID_PRODUTO)
);
```
![Image](https://bn1301files.storage.live.com/y4mF6H61V25blS9-78dv_jXgRLohDM_sO6IXcrIC62N6RRgGmhhGCM-nE6B7GgieU9qNRkKPlUg_0Fnv1LFf92jcn0EBz4LcMx27x05ugoHMZauXcNVAEW07vqPLo9AI0rPwCb2n2Do3MIT_uUNCNCk335mPEod4nmHzLYQb1FR19hi9-9Dlyw8wRa1kBB8kpPf?width=1271&height=637&cropmode=none)

**Referências**
https://docs.oracle.com/cd/E17952_01/mysql-5.6-en/create-table-foreign-keys.html
