---
title: "Como Criar um Blog Como Esse"
date: 2024-01-01 00:00:00:0000
categories: [Blog]
tags: [blog]
---

# Hello World
Fala, galera! 

Lorenzo está de cara nova agora, faz tempo que estava querendo criar um Blog e fiquei impressionado com a facilidade de criação desse Blog.

Como o intuito desse Blog é espalha conhecimento, vou começar esse post ensinando e documentando como criar um Blog como esse.

# O Que é o Jekyll?
Jekyll é um gerador de sites estáticos escrito em Ruby. Ele permite criar sites e blogs que são transformados em arquivos HTML estáticos. A principal vantagem do Jekyll é que ele permite desenvolver sites de forma simples, sem a necessidade de um banco de dados ou backend dinâmico. 

**Alguns benefícios do Jekyll incluem:**
1. **Simplicidade:** Ideal para sites simples como blogs, portfólios e documentações.
2. **Hospedagem no GitHub Pages:** Jekyll é integrado ao GitHub Pages, permitindo que desenvolvedores hospedem seus sites diretamente no GitHub.
3. **Markdown e Layouts:** Suporte a escrita em Markdown e a utilização de layouts para fácil formatação de conteúdo.

**Porque usar o Jenkyll como motor de blog?**

Para responder essa, vou deixar um link explicando alguns motivos.
- https://aleatorio.dev.br/posts/porque-usar-jenkyl/

*Esse blog post foi feito no Jenkyll também*

# Instalação e Setup
Seu primeiro passo aqui é clonar é usar o template do GitHub e depois clonar para um repositório próprio: [Link Template GitHub](https://github.com/cotes2020/chirpy-starter)

Clique em:
1. Use This Template
2. Create a New Repository
3. O nome do repositório precisa ser assim: githubusername.github.io *(O meu é lorenzouriel.github.io)*
4. Clone o seu repositório em uma pasta local

Para iniciar, você precisa instalar o Ruby e o Jekyll na sua máquina, existe um tutorial para isso:[Tutorial](https://jekyllrb.com/docs/installation/)

Depois que tiver instalado, você pode rodar os seguintes comandos:
- `bundle` - Instalar as últimas dependências
- `bundle exec jekyll s` - Realizar o Build e rodar localmente

Depois que você adicionar o seu post, pode fazer o push para o repositório remoto, por conta do arquivo `pages-deploy.yml` o deploy é realizado automaticamente no seu GitHub pages, você pode acessar ele atráves do nome do repositório que você criou: githubusername.github.io *(O meu é [lorenzouriel.github.io](https://lorenzouriel.github.io/))*

# Criando o Primeiro Post
Achei legal adicionar esse tópico porque existe uma estrutura padrão para a criação de um Blog no Jenkyll.

Você deve criar o arquivo Markdown na pasta: `_posts`. Existe um padrão para a criação do arquivo, precisa ser:`yyyy-mm-dd-titulo-post.md`.

Após a criação do arquivo, você deve iniciar o Markdown com algumas variáveis.
```md
---
title: "Como Criar um Blog Como Esse"
date: 2024-01-01 00:00:00:0000
categories: [Blog]
tags: [blog]
---

# Hello World
Fala, galera! 
```

Quando fechar com os `---`, você pode iniciar o seu post em Markdown.

Depois é só fazer o deploy e acessar.

# Referências
- Tutorial: https://www.youtube.com/watch?v=m1RYsmOMPLs
- Instalação: https://jekyllrb.com/docs/installation/windows/
- Dicas de Customização: https://chirpy.cotes.page/posts/customize-the-favicon/
- Mais Sobre o Jenkyll: https://jekyllrb.com/