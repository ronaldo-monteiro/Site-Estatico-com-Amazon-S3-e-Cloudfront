# Hospedando um Site Estático em um Bucket S3 com CloudFront Usando Terraform

## Visão Geral
Este guia mostra como configurar a infraestrutura para hospedar um site estático em um bucket S3 e distribuir seu conteúdo globalmente com o CloudFront. A configuração é realizada utilizando Terraform, uma ferramenta IaC (Infrastructure as Code).

### Recursos que serão criados
1. Bucket S3 para armazenar os arquivos do site estático.
2. Configuração de política de bucket para permitir acesso público ao conteúdo do site.
3. Distribuição CloudFront para entregar o site de forma otimizada globalmente.
4. Configuração de um certificado SSL usando o AWS Certificate Manager (opcional).
5. Configuração do Terraform para gerenciar a infraestrutura.

---

## Pré-requisitos
- Conta na AWS.
- Instalação do Terraform na sua máquina.
- Permissões necessárias para criar recursos no AWS S3, CloudFront e ACM.

---

## Passos

### 1. Configurar o Bucket S3

Crie um bucket S3 para armazenar os arquivos do site estático.

```hcl
resource "aws_s3_bucket" "site_bucket" {
  bucket        = "meu-site-estatico"
  acl           = "public-read"
  website {
    index_document = "index.html"
    error_document = "error.html"
  }
}

resource "aws_s3_bucket_policy" "site_bucket_policy" {
  bucket = aws_s3_bucket.site_bucket.id

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Effect    = "Allow"
        Principal = "*"
        Action    = "s3:GetObject"
        Resource  = "${aws_s3_bucket.site_bucket.arn}/*"
      }
    ]
  })
}
