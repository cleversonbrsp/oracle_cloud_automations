# OCI AUTO TAG

### Autor: Cleverson Rodrigues  
### Data: 22/12/2024  

### Suporte:
- Compatível com Python 3

---

### **Aviso Legal:**
Este não é um aplicativo oficial da Oracle. Não é suportado pelo Oracle Support e **NÃO** deve ser utilizado para fins de cálculo de utilização.

---

## **Informações:**
Script para marcar recursos em um tenancy.

---

## **Conectividade:**
1. **Autenticação de Usuário**  
   Utilize `$HOME/.oci/config`.  
   Consulte: [SDK Configuration Documentation](https://docs.cloud.oracle.com/en-us/iaas/Content/API/Concepts/sdkconfig.htm)
2. **Princípios de Instância**  
3. **Delegação de Token no Cloud Shell**  

---

## **Módulos Incluídos:**
- `oci.core.ComputeClient`
- `oci.core.BlockstorageClient`
- `oci.core.VirtualNetworkClient`
- `oci.identity.IdentityClient`
- `oci.load_balancer.LoadBalancerClient`
- `oci.database.DatabaseClient`
- `oci.object_storage.ObjectStorageClient`

---

## **Análise do Script Python para Gerenciamento de Tags na OCI**

Este script em Python é projetado para gerenciar tags de recursos na Oracle Cloud Infrastructure (OCI). Abaixo está uma explicação detalhada de suas principais seções e funcionalidades.

### Importações

```python
import sys
import argparse
import datetime
import oci
import json
import os
```

- O script importa bibliotecas necessárias: `sys` para manipulação de argumentos de linha de comando, `argparse` para processamento de argumentos, `datetime` para timestamps, `oci` para interagir com a OCI, `json` para formatação de saída e `os` para operações do sistema.

### Variáveis Globais

```python
errors = 0
data = []
cmd = ""
```

- `errors`: Contador para rastrear erros durante a execução.
- `data`: Lista para armazenar informações sobre as operações realizadas.
- `cmd`: Variável para armazenar os comandos de linha de comando processados.

### Funções Principais

#### 1. Exibição de Banner

```python
def print_banner(cmd, tenancy, assign_tags):
    ...
```

- Exibe um banner informativo sobre a execução do script, incluindo informações sobre o autor, hora de início e detalhes dos comandos.

#### 2. Processamento de Argumentos

```python
def command_line():
    ...
```

- Configura o parser de argumentos utilizando `argparse`, permitindo ao usuário especificar opções como perfil de configuração, proxy, compartimentos, regiões e ações a serem executadas (adicionar, deletar ou listar tags).

#### 3. Criação de Signatário

```python
def create_signer(config_profile, is_instance_principals, is_delegation_token):
    ...
```

- Cria um objeto de autenticação (`signer`) com base nas credenciais fornecidas. Suporta autenticação através de Instância, Token de Delegação ou arquivo de configuração.

#### 4. Verificação de Namespaces de Tags

```python
def read_tag_namespaces(identity, tenancy, local_assign_tags):
    ...
```

- Verifica se os namespaces de tags especificados existem na OCI, abortando a execução se algum namespace não for encontrado.

#### 5. Carregamento de Compartimentos

```python
def identity_read_compartments(identity, tenancy):
    ...
```

- Carrega os compartimentos disponíveis na OCI e filtra conforme necessário.

#### 6. Manipulação de Objetos

```python
def handle_object(compartment, region_name, assign_tags, obj_name, list_object, update_object, update_modal_obj, availability_domains=None, namespace="", filter_by_name=""):
    ...
```

- Realiza as operações de gerenciamento de tags em diferentes tipos de recursos, como instâncias, volumes e balanceadores de carga. Atualiza as tags conforme a ação especificada.

#### 7. Manipulação de Tags

```python
def handle_tags(defined_tags, freeform_tags, local_assign_tags):
    ...
```

- Adiciona, atualiza ou remove tags, tanto definidas quanto livres, de acordo com as instruções do usuário.

### Uso do Comando

Para filtrar e taguear apenas recursos que contenham o nome "mkd", você pode usar o seguinte comando:

```bash
python3 auto_tag.py -t DEFAULT -action add_defined -tag "pythonzero.autotag=hml" -filter_by_name "mkd"
```
- **Parâmetros**:
  - `-t DEFAULT`: Especifica o perfil de configuração a ser usado.
  - `-action add_defined`: Ação para adicionar tags definidas.
  - `-tag "pythonzero.autotag=hml"`: Define a tag a ser aplicada aos recursos filtrados.
  - `-filter_by_name "mkd"`: Filtra os recursos que contêm "mkd" em seus nomes.

### Função Principal

```python
def main():
    ...
```

- Função principal que orquestra a execução do script. Conecta-se à OCI, processa os argumentos de linha de comando, verifica namespaces de tags e itera sobre regiões e compartimentos para aplicar as operações de tagging.

### Saída

- O script exibe a saída em formato JSON ou uma lista, dependendo da opção especificada pelo usuário. Ele também reporta quaisquer erros que ocorreram durante a execução.

### Execução do Script

```python
main()
```

- Chama a função `main()` para iniciar a execução do script.

---

## **Parâmetros da Linha de Comando:**

| Parâmetro        | Descrição                                                                                      |
|-------------------|----------------------------------------------------------------------------------------------|
| `-t config`       | Arquivo de configuração a ser usado (perfil de tenancy).                                      |
| `-p proxy`        | Configurar proxy (exemplo: `www-proxy-server.com:80`).                                       |
| `-ip`             | Utilizar Princípios de Instância para autenticação.                                          |
| `-dt`             | Utilizar Princípios de Instância com token de delegação no Cloud Shell.                      |
| `-cp compartment` | Filtrar por nome ou ID do compartimento.                                                     |
| `-rg region`      | Filtrar por nome da região.                                                                  |
| `-action`         | Ação a ser realizada: `add_defined`, `add_free`, `del_defined`, `del_free`, ou `list`.       |
| `-tag`            | Informações da tag no formato `namespace.key=value` ou `key=value`. Separe múltiplas tags com vírgula. |
| `-tagsep`         | Separador de tags (padrão: vírgula).                                                         |
| `-service type`   | Tipo de serviço (padrão: `all`). Serviços disponíveis: `all`, `compute`, `block`, `network`, `identity`, `loadbalancer`, `database`, `object`, `file`. |
| `-output`         | Formato de saída: `list`, `json`, ou `summary`.                                              |
| `-filter_by_name` | Filtrar serviços por nome. Separe múltiplos nomes com vírgula.                               |

---