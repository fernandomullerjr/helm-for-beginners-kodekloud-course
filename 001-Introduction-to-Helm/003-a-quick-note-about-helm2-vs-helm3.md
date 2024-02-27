
# ###################################################################################################################### 
# ###################################################################################################################### 
#  push

git status
git add .
git commit -m " a-quick-note-about-helm2-vs-helm3."
git push
git status



# ###################################################################################################################### 
# ###################################################################################################################### 
## a-quick-note-about-helm2-vs-helm3

- Tiller era usado no Helm2.
        Tiller tinha muitos privilégios, RBAC, etc

- O Helm 3 é mais inteligente, porque usa o 3-way merge.
        Sem o 3-way, o Helm 2 não entendia que havia uma nova revision, caso fosse realizado um ajuste via kubectl set, por exemplo.





# ###################################################################################################################### 
# ###################################################################################################################### 
##  Detalhes sobre  "3-way merge"

## GPT

O "3-way merge" no Helm 3 é uma abordagem usada para mesclar valores de configuração em um ambiente de implantação do Kubernetes. Quando você usa o Helm para implantar um gráfico, ele pode mesclar três fontes diferentes de valores de configuração:

    Valores padrão do gráfico: Estes são os valores de configuração padrão fornecidos pelo desenvolvedor do gráfico. Eles geralmente estão contidos no arquivo values.yaml dentro do gráfico.

    Valores especificados pelo usuário: Estes são os valores de configuração que você fornece ao executar o comando helm install ou helm upgrade. Eles substituem os valores padrão do gráfico.

    Valores atuais no cluster Kubernetes: Estes são os valores de configuração atualmente em uso no cluster para a release que está sendo atualizada ou instalada. Eles podem ser derivados de uma instalação anterior do Helm ou de alterações manuais feitas diretamente nos recursos Kubernetes.

O processo de "3-way merge" funciona assim:

    Valores padrão e valores do usuário: O Helm mescla os valores padrão do gráfico com os valores especificados pelo usuário. Se houver conflitos entre os valores, os valores do usuário substituirão os padrões.

    Valores atuais no cluster Kubernetes: O Helm compara os valores resultantes do passo anterior com os valores atualmente em uso no cluster Kubernetes. Ele mescla esses valores, aplicando qualquer alteração feita no cluster desde a última instalação ou atualização.

    Resultado final: O resultado é uma fusão dos três conjuntos de valores de configuração, onde os valores do usuário têm precedência sobre os padrões do gráfico, e as alterações feitas no cluster têm precedência sobre ambos.

Esse processo garante que você possa personalizar as implantações de gráficos Helm usando valores específicos do usuário, enquanto ainda mantém quaisquer alterações feitas manualmente ou por implantações anteriores no cluster Kubernetes.



## Gemini

Como funciona o 3-way merge do Helm 3?

O 3-way merge do Helm 3 é um processo que combina três arquivos de manifesto do Kubernetes:

    Manifesto antigo: O manifesto da versão anterior do lançamento do Helm.
    Manifesto atual: O manifesto da versão atual do gráfico do Helm.
    Manifesto em tempo real: O estado real dos recursos do Kubernetes implantados.

O Helm 3 usa um algoritmo de mesclagem de três vias para gerar um novo manifesto que combina as alterações do manifesto atual com o estado real dos recursos do Kubernetes. O novo manifesto é então aplicado ao cluster do Kubernetes para atualizar a implantação.

Processo de mesclagem:

    O Helm 3 compara o manifesto antigo com o manifesto atual para identificar as alterações.
    O Helm 3 compara o manifesto atual com o manifesto em tempo real para identificar as alterações.
    O Helm 3 usa um algoritmo de mesclagem de três vias para combinar as alterações dos dois conjuntos de alterações.
    O Helm 3 gera um novo manifesto que incorpora as alterações mescladas.
    O Helm 3 aplica o novo manifesto ao cluster do Kubernetes para atualizar a implantação.

Vantagens do 3-way merge:

    Gerenciamento de conflito: O 3-way merge pode detectar e resolver automaticamente conflitos entre os manifestos.
    Atualizações seguras: O 3-way merge garante que as atualizações sejam aplicadas de forma segura e sem causar interrupções na implantação.
    Histórico de implantação linear: O 3-way merge mantém um histórico de implantação linear, facilitando a retrocesso para uma versão anterior.

Limitações do 3-way merge:

    Complexidade: O 3-way merge pode ser um processo complexo, especialmente para implantações complexas.
    Desafios de resolução de conflitos: Em alguns casos, o 3-way merge pode não ser capaz de resolver automaticamente conflitos entre os manifestos.

Recursos adicionais:

    Documentação do Helm 3: https://helm.sh/docs/helm/






## PENDENTE
- Continuar video.