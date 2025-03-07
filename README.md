Descrição Técnica do Código Original

O arquivo main.tf original cria os seguintes recursos na AWS:

    Provider AWS:

        Configura o provedor AWS para a região us-east-1.

    Variáveis:Descrição Técnica do Código Original

O arquivo main.tf original cria os seguintes recursos na AWS:

    Provider AWS:

        Configura o provedor AWS para a região us-east-1.

    Variáveis:

        projeto: Define o nome do projeto, com valor padrão "VExpenses".

        candidato: Define o nome do candidato, com valor padrão "SeuNome".

        ssh_allowed_ip: Define o endereço IP permitido para acesso SSH, com valor padrão 0.0.0.0/0 (acesso aberto a qualquer IP).

        instance_type: Define o tipo de instância EC2, com valor padrão t2.micro.

    Chave SSH:

        Gera um par de chaves SSH (tls_private_key.ec2_key) e cria um Key Pair na AWS (aws_key_pair.ec2_key_pair) para acessar a instância EC2.

    VPC (Virtual Private Cloud):

        Cria uma VPC (aws_vpc.main_vpc) com o bloco CIDR 10.0.0.0/16 e habilita suporte a DNS.

    Subnet:

        Cria uma subnet (aws_subnet.main_subnet) dentro da VPC, com o bloco CIDR 10.0.1.0/24 na zona de disponibilidade us-east-1a.

    Internet Gateway:

        Cria um Internet Gateway (aws_internet_gateway.main_igw) e o associa à VPC.

    Tabela de Rotas:

        Cria uma tabela de rotas (aws_route_table.main_route_table) com uma rota padrão (0.0.0.0/0) apontando para o Internet Gateway.

        Associa a tabela de rotas à subnet (aws_route_table_association.main_association).

    Grupo de Segurança:

        Cria um grupo de segurança (aws_security_group.main_sg) que permite:

            Acesso SSH na porta 22 a partir do IP definido em ssh_allowed_ip.

            Tráfego HTTP na porta 80 e HTTPS na porta 443 a partir de qualquer IP.

            Todo o tráfego de saída (egress) para qualquer IP.

    Instância EC2:

        Cria uma instância EC2 (aws_instance.debian_ec2) usando uma AMI do Debian 12.

        A instância é do tipo t2.micro, associada à subnet criada, e utiliza o Key Pair gerado.

        Configura um volume raiz de 20 GB do tipo gp2.

        Usa user_data para instalar e iniciar o Nginx automaticamente após a criação da instância.

    Outputs:

        private_key: Exibe a chave privada gerada para acessar a instância EC2 (sensível).

        ec2_public_ip: Exibe o endereço IP público da instância EC2.

Observações sobre o Código Original

    Segurança:

        O grupo de segurança permitia acesso SSH a partir de qualquer IP (0.0.0.0/0), o que é um risco de segurança.

        Não havia regras para tráfego HTTP ou HTTPS, o que limitava a usabilidade da instância para aplicações web.

    Automação:

        O user_data apenas atualizava o sistema, sem instalar nenhum serviço adicional, como o Nginx.

    Organização:

        O código era bem organizado, mas poderia ser mais flexível com o uso de variáveis para configurações como o tipo de instância.

Modificação e Melhoria do Código Terraform 
Melhorias Implementadas
1. Melhorias de Segurança

    Restrição de Acesso SSH:

        O acesso SSH foi restrito a um IP específico, definido pela variável ssh_allowed_ip. Isso reduz a superfície de ataque, permitindo que apenas endereços IP confiáveis se conectem à instância.

        Impacto esperado: Apenas usuários com o IP autorizado poderão acessar a instância via SSH.

    Permissão de Tráfego HTTP e HTTPS:

        Foram adicionadas regras para permitir tráfego HTTP (porta 80) e HTTPS (porta 443) a partir de qualquer IP (0.0.0.0/0).

        Impacto esperado: A instância estará acessível via navegador, permitindo a hospedagem de aplicações web.

2. Automação da Instalação do Nginx

    O que foi feito:

        O user_data foi modificado para instalar e iniciar o Nginx automaticamente após a criação da instância. O script executa os seguintes comandos:

            Atualiza o sistema (apt-get update -y e apt-get upgrade -y).

            Instala o Nginx (apt-get install -y nginx).

            Inicia e habilita o Nginx (systemctl start nginx e systemctl enable nginx).

    Por que foi feito:

        A automação da instalação do Nginx garante que a instância esteja pronta para uso imediatamente após a criação, sem necessidade de intervenção manual.

3. Outras Melhorias

    Variáveis Adicionais:

        Adicionamos a variável instance_type para permitir a personalização do tipo de instância EC2. Isso torna o código mais flexível e reutilizável.

    Tags:

        Adicionadas tags adicionais (Environment, Project, Candidate) para melhorar a organização e a rastreabilidade dos recursos na AWS.

Instruções de Uso
Pré-requisitos

    Terraform:

        Ter terraform instalado, você pode baixá-lo em terraform.io.

    Credenciais da AWS:

        Configure as credenciais da AWS usando o AWS CLI ou variáveis de ambiente:
        
        export AWS_ACCESS_KEY_ID="sua_access_key"
        export AWS_SECRET_ACCESS_KEY="sua_secret_key"

Passos para Execução

    Clone o Repositório:

    git clone <repositório>
    cd <diretório-do-repositório>

    Inicialize o Terraform:

    terraform init

    Aplique a Configuração:

    terraform apply

    Acesse a Instância EC2:

        Use o IP público e a chave privada gerada para acessar a instância via SSH:

        ssh -i chave_privada.pem debian@<ip_publico>

    Verifique o Nginx:

        Abra um navegador e acesse o IP público da instância. Você deve ver a página padrão do Nginx.

Destruição da Infraestrutura

    Para remover todos os recursos criados e evitar custos desnecessários, execute:

    terraform destroy

        projeto: Define o nome do projeto, com valor padrão "VExpenses".

        candidato: Define o nome do candidato, com valor padrão "SeuNome".

        ssh_allowed_ip: Define o endereço IP permitido para acesso SSH, com valor padrão 0.0.0.0/0 (acesso aberto a qualquer IP).

        instance_type: Define o tipo de instância EC2, com valor padrão t2.micro.

    Chave SSH:

        Gera um par de chaves SSH (tls_private_key.ec2_key) e cria um Key Pair na AWS (aws_key_pair.ec2_key_pair) para acessar a instância EC2.

    VPC (Virtual Private Cloud):

        Cria uma VPC (aws_vpc.main_vpc) com o bloco CIDR 10.0.0.0/16 e habilita suporte a DNS.

    Subnet:

        Cria uma subnet (aws_subnet.main_subnet) dentro da VPC, com o bloco CIDR 10.0.1.0/24 na zona de disponibilidade us-east-1a.

    Internet Gateway:

        Cria um Internet Gateway (aws_internet_gateway.main_igw) e o associa à VPC.

    Tabela de Rotas:

        Cria uma tabela de rotas (aws_route_table.main_route_table) com uma rota padrão (0.0.0.0/0) apontando para o Internet Gateway.

        Associa a tabela de rotas à subnet (aws_route_table_association.main_association).

    Grupo de Segurança:

        Cria um grupo de segurança (aws_security_group.main_sg) que permite:

            Acesso SSH na porta 22 a partir do IP definido em ssh_allowed_ip.

            Tráfego HTTP na porta 80 e HTTPS na porta 443 a partir de qualquer IP.

            Todo o tráfego de saída (egress) para qualquer IP.

    Instância EC2:

        Cria uma instância EC2 (aws_instance.debian_ec2) usando uma AMI do Debian 12.

        A instância é do tipo t2.micro, associada à subnet criada, e utiliza o Key Pair gerado.

        Configura um volume raiz de 20 GB do tipo gp2.

        Usa user_data para instalar e iniciar o Nginx automaticamente após a criação da instância.

    Outputs:

        private_key: Exibe a chave privada gerada para acessar a instância EC2 (sensível).

        ec2_public_ip: Exibe o endereço IP público da instância EC2.

Observações sobre o Código Original

    Segurança:

        O grupo de segurança permitia acesso SSH a partir de qualquer IP (0.0.0.0/0), o que é um risco de segurança.

        Não havia regras para tráfego HTTP ou HTTPS, o que limitava a usabilidade da instância para aplicações web.

    Automação:

        O user_data apenas atualizava o sistema, sem instalar nenhum serviço adicional, como o Nginx.

    Organização:

        O código era bem organizado, mas poderia ser mais flexível com o uso de variáveis para configurações como o tipo de instância.

Modificação e Melhoria do Código Terraform 
Melhorias Implementadas
1. Melhorias de Segurança

    Restrição de Acesso SSH:

        O acesso SSH foi restrito a um IP específico, definido pela variável ssh_allowed_ip. Isso reduz a superfície de ataque, permitindo que apenas endereços IP confiáveis se conectem à instância.

        Impacto esperado: Apenas usuários com o IP autorizado poderão acessar a instância via SSH.

    Permissão de Tráfego HTTP e HTTPS:

        Foram adicionadas regras para permitir tráfego HTTP (porta 80) e HTTPS (porta 443) a partir de qualquer IP (0.0.0.0/0).

        Impacto esperado: A instância estará acessível via navegador, permitindo a hospedagem de aplicações web.

2. Automação da Instalação do Nginx

    O que foi feito:

        O user_data foi modificado para instalar e iniciar o Nginx automaticamente após a criação da instância. O script executa os seguintes comandos:

            Atualiza o sistema (apt-get update -y e apt-get upgrade -y).

            Instala o Nginx (apt-get install -y nginx).

            Inicia e habilita o Nginx (systemctl start nginx e systemctl enable nginx).

    Por que foi feito:

        A automação da instalação do Nginx garante que a instância esteja pronta para uso imediatamente após a criação, sem necessidade de intervenção manual.

3. Outras Melhorias

    Variáveis Adicionais:

        Adicionamos a variável instance_type para permitir a personalização do tipo de instância EC2. Isso torna o código mais flexível e reutilizável.

    Tags:

        Adicionadas tags adicionais (Environment, Project, Candidate) para melhorar a organização e a rastreabilidade dos recursos na AWS.

Instruções de Uso
Pré-requisitos

    Terraform:

        Ter terraform instalado, você pode baixá-lo em terraform.io.

    Credenciais da AWS:

        Configure as credenciais da AWS usando o AWS CLI ou variáveis de ambiente:
        
        export AWS_ACCESS_KEY_ID="sua_access_key"
        export AWS_SECRET_ACCESS_KEY="sua_secret_key"

Passos para Execução

    Clone o Repositório:

    git clone <repositório>
    cd <diretório-do-repositório>

    Inicialize o Terraform:

    terraform init

    Aplique a Configuração:

    terraform apply

    Acesse a Instância EC2:

        Use o IP público e a chave privada gerada para acessar a instância via SSH:

        ssh -i chave_privada.pem debian@<ip_publico>

    Verifique o Nginx:

        Abra um navegador e acesse o IP público da instância. Você deve ver a página padrão do Nginx.

Destruição da Infraestrutura

    Para remover todos os recursos criados e evitar custos desnecessários, execute:

    terraform destroy
