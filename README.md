# bd_tcc

-- drop database ecommerce_carros;

create database ecommerce_carros;
use ecommerce_carros;

create table tbEstado (
    UFID int primary key auto_increment,
    UF char(2) not null
);

create table tbCidade (
    CidadeID int primary key auto_increment,
    Cidade varchar(200) not null,
    UFID int not null,
    foreign key (UFID) references tbEstado(UFID)
);

create table tbBairro (
    BairroID int primary key auto_increment,
    Bairro varchar(200) not null,
    CidadeID int not null,
    foreign key (CidadeID) references tbCidade(CidadeID)
);

create table tbEndereco (
    logradouro varchar(200) not null,
    CEP numeric(8) primary key,
    BairroID int not null,
    CidadeID int not null,
    UFID int not null,
    foreign key (BairroID) references tbBairro(BairroID),
    foreign key (CidadeID) references tbCidade(CidadeID),
    foreign key (UFID) references tbEstado(UFID)
);

create table telefone (
    id_telefone int auto_increment primary key,
    id_cliente int not null,
    telefone varchar(20),
    celular varchar(20)
);

create table clientes (
    id_cliente int auto_increment,
    nome varchar(100) not null,
    sobrenome varchar(100) not null,
    email varchar(150) not null,
    senha varchar(255) not null,
    id_endereco int,  
    id_telefone int,
    tipo_cliente enum('pf', 'pj') default 'pf',
    data_cadastro timestamp default current_timestamp,
    CepCli numeric(8),  
    primary key (id_cliente),
    unique key (email),
    foreign key (id_endereco) references tbEndereco(CEP), 
    foreign key (id_telefone) references telefone(id_telefone),
    foreign key (CepCli) references tbEndereco(CEP)  
);

create table categorias (
    id_categoria int primary key,
    descricao varchar(50) not null
);

insert into categorias (id_categoria, descricao) values 
(1, 'Híbridos'),
(2, 'Elétricos');

create table carregadores (
    id_carregador int primary key,
    modelo varchar(50) not null,
    marca varchar(50) not null,
    potencia int not null,
    preco decimal(10,2) not null
);

insert into carregadores (id_carregador, modelo, marca, potencia, preco) values 
(1, 'Wallbox', 'EVBox', 22, 1500.00),
(2, 'Home Charger', 'ChargerPoint', 32, 2500.00),
(3, 'Fast Charger', 'ABB', 50, 5000.00),
(4, 'Portable Charger', 'Webasto', 10, 1000.00),
(5, 'Smart Charger', 'Siemens', 22, 2000.00);

create table carros (
    id_carro int primary key,
    modelo varchar(50) not null,
    marca varchar(50) not null,
    ano int not null,
    preco decimal(10,2) not null,
    id_categoria int not null,
    id_carregador int not null,
    foreign key (id_categoria) references categorias(id_categoria),
    foreign key (id_carregador) references carregadores(id_carregador)
);

insert into carros (id_carro, modelo, marca, ano, preco, id_categoria, id_carregador) values
(1, 'Prius', 'Toyota', 2022, 80000.00, 1, 1),
(2, 'Civic Hybrid', 'Honda', 2021, 70000.00, 1, 1),
(3, 'Corolla Hybrid', 'Toyota', 2020, 65000.00, 1, 1),
(4, 'Insight Hybrid', 'Honda', 2019, 60000.00, 1, 1),
(5, 'Auris Hybrid', 'Toyota', 2018, 55000.00, 1, 1),
(6, 'Leaf', 'Nissan', 2022, 90000.00, 2, 2),
(7, 'e-Golf', 'Volkswagen', 2021, 85000.00, 2, 2),
(8, 'Model 3', 'Tesla', 2019, 100000.00, 2, 3),
(9, 'i3', 'BMW', 2018, 80000.00, 2, 4);

create table pedidos (
    id_pedido int primary key,
    id_cliente int not null,
    data_pedido date not null,
    valor_total decimal(10,2) not null,
    status_pedido varchar(50) not null,
    foreign key (id_cliente) references clientes(id_cliente)
);

create table itens_pedidos (
    id_item_pedido int primary key,
    id_pedido int not null,
    id_carro int,
    id_carregador int,
    quantidade int not null,
    preco_unitario decimal(10,2) not null,
    foreign key (id_pedido) references pedidos(id_pedido),
    foreign key (id_carro) references carros(id_carro),
    foreign key (id_carregador) references carregadores(id_carregador)
);

create table test_drive (
    cod int auto_increment primary key,
    id_cliente int,
    id_carro int,
    data_test datetime not null,
    foreign key (id_cliente) references clientes(id_cliente),
    foreign key (id_carro) references carros(id_carro)
);

create table cartoes (
    id_cartao int auto_increment primary key,
    id_cliente int not null,
    numero_cartao varchar(19) not null,
    nome_titular varchar(100) not null,
    validade varchar(5) not null,
    cvv varchar(4) not null,
    bandeira varchar(20),
    foreign key (id_cliente) references clientes(id_cliente)
);

create table FormaPagamento (
    id_pagamento int auto_increment primary key,
    id_pedido int not null,
    forma_pagamento enum('cartao_credito', 'boleto', 'pix', 'debito_online') not null,
    id_cartao int,
    codigo_pix varchar(100),
    valor_pago decimal(10,2) not null,
    foreign key (id_pedido) references pedidos(id_pedido),
    foreign key (id_cartao) references cartoes(id_cartao)
);

create table NotaFiscal (
    id_nota_fiscal int auto_increment primary key,
    id_pagamento int not null,
    numero_nota varchar(50) not null,
    data_emissao date not null,
    valor_total decimal(10,2) not null,
    chave_acesso varchar(100) not null,
    foreign key (id_pagamento) references FormaPagamento(id_pagamento)
);

create table avaliacoes (
    id_avaliacao int auto_increment primary key,
    id_cliente int not null,
    id_pedido int not null,
    avaliacao_escrita varchar(300),
    avaliacao_nota decimal(3,2) default 0.0,
    data_avaliacao datetime default current_timestamp,
    foreign key (id_cliente) references clientes(id_cliente),
    foreign key (id_pedido) references pedidos(id_pedido)
);
