# backup_organizado.sh

Script em Bash que organiza arquivos por tipo, permite revisão manual antes de compactar, e salva o backup em um HD externo escolhido por você em um menu interativo.

## Funcionalidades

- **Escolha do HD por menu**: detecta automaticamente os dispositivos montados em `/media`, `/run/media` e `/mnt`, e permite escolher o destino por número (ou digitar o caminho manualmente).
- **Organização automática por tipo**: classifica os arquivos em categorias (Documentos, Planilhas, Apresentacoes, Imagens, Videos, Audios, Compactados, Codigo, Outros) com base na extensão.
- **Pausa para revisão manual**: antes de compactar, o script para e permite abrir a pasta temporária no gerenciador de arquivos para remover, mover ou adicionar itens.
- **Indicadores de progresso**:
  - spinner giratório enquanto procura os HDs conectados;
  - barra de progresso (estilo blocos `▰▰▰░░░`) durante a cópia dos arquivos;
  - spinner giratório durante a compactação e gravação no HD.
- **Backup compactado**: gera um `.zip` com data e hora no nome, salvo em `Backups/` dentro do HD escolhido.

## Requisitos

- Linux com Bash.
- Pacote `zip` instalado (`sudo apt install zip` no Ubuntu/Debian).
- `tput` (geralmente já vem instalado) — usado para esconder/mostrar o cursor durante os spinners; se não existir, o script continua funcionando normalmente, só sem esse efeito.

## Instalação

Antes de executar o script pela primeira vez, é preciso dar a ele permissão de execução:

```bash
chmod +x backup_organizado.sh
```

**Por que isso é necessário:** no Linux, um arquivo só pode ser executado diretamente se tiver a permissão de execução ativada. Por padrão, um arquivo baixado ou criado normalmente não tem essa permissão — ele pode ser lido e editado, mas não "rodado" como um programa. O comando `chmod` (*change mode*) altera essas permissões, e o `+x` adiciona (`+`) a permissão de execução (`x`, de *execute*).

Você pode conferir as permissões atuais do arquivo com:

```bash
ls -l backup_organizado.sh
```

Antes do `chmod +x`, a saída começa assim (sem nenhum `x`):

```
-rw-r--r-- 1 usuario usuario 4521 set 30 10:00 backup_organizado.sh
```

Depois do `chmod +x`, aparece o `x` nas três posições de permissão (dono, grupo e outros):

```
-rwxr-xr-x 1 usuario usuario 4521 set 30 10:00 backup_organizado.sh
```

Esse passo só precisa ser feito **uma vez** — a permissão fica salva no arquivo. Se você mover ou copiar o script para outro lugar, a permissão é mantida; mas se você recriar o arquivo do zero (por exemplo, colando o conteúdo em um arquivo novo), vai precisar rodar o `chmod +x` de novo.

Se você tentar executar o script sem essa permissão, vai aparecer o erro:

```
bash: ./backup_organizado.sh: Permission denied
```

Nesse caso, basta rodar o `chmod +x` e tentar de novo.

## Configuração

Abra o arquivo em um editor de texto e ajuste o início, se necessário:

```bash
ORIGENS=(
    "/home/$USER/Documentos"
    "/home/$USER/Downloads"
    "/home/$USER/Músicas"
    "/home/$USER/Imagens"
    "/home/$USER/Vídeos"
)
```

São as pastas que entram no backup. Adicione ou remova linhas conforme sua necessidade.

> **Atenção:** em sistemas com o idioma em inglês, essas pastas costumam se chamar `Documents`, `Downloads`, `Music`, `Pictures` e `Videos`, sem acento. Confira os nomes reais com `ls ~/` antes de rodar.

Não é necessário configurar o HD de destino no código — isso é feito pelo menu ao executar o script.

## Como usar

1. Conecte o HD externo e espere ele montar.
2. Execute o script:
   ```bash
   ./backup_organizado.sh
   ```
3. Escolha o HD de destino no menu numerado.
4. Aguarde a cópia e organização automática dos arquivos (barra de progresso).
5. Quando o script pausar, revise a pasta temporária: remova, mova ou adicione arquivos à vontade.
6. Pressione **ENTER** para continuar.
7. Aguarde a compactação e gravação no HD (spinner).
8. Ao final, o script lista todos os backups já existentes na pasta `Backups` do HD.

## Categorias de arquivos

| Categoria       | Extensões                                              |
|-----------------|---------------------------------------------------------|
| Documentos      | pdf, doc, docx, odt, txt, rtf                           |
| Planilhas       | xls, xlsx, ods, csv                                     |
| Apresentacoes   | ppt, pptx, odp                                          |
| Imagens         | jpg, jpeg, png, gif, bmp, svg, webp, tiff, heic         |
| Videos          | mp4, mkv, avi, mov, wmv, flv, webm                      |
| Audios          | mp3, wav, flac, ogg, aac, m4a                           |
| Compactados     | zip, rar, 7z, tar, gz, bz2, xz                          |
| Codigo          | py, js, html, css, c, cpp, java, sh, json, xml, php, ts |
| Outros          | qualquer extensão não listada acima                     |

## Estrutura gerada no HD

```
HD/
└── Backups/
    ├── backup_2026-09-28_14-30.zip
    ├── backup_2026-09-29_09-12.zip
    └── ...
```

## Limitações conhecidas

- **Arquivos com o mesmo nome** vindos de pastas de origem diferentes podem se perder: a cópia usa `cp -n` (não sobrescreve), então só o primeiro arquivo copiado para cada categoria é mantido.
- **A estrutura de pastas original é perdida**: os arquivos são agrupados só por tipo, não por pasta de origem.
- **Espaço em `/tmp`**: os arquivos são copiados para uma pasta temporária antes de compactar, então é necessário espaço livre suficiente nesse local.

## Testando sem usar o HD de verdade

Para testar o script sem mexer nos seus arquivos reais, crie um ambiente de teste:

```bash
mkdir -p ~/teste_backup/Documentos ~/teste_backup/Imagens ~/teste_backup/Downloads
touch ~/teste_backup/Documentos/exemplo.pdf
mkdir -p ~/HD_falso
```

Depois, no menu do script, escolha "Digitar o caminho manualmente" e informe `~/HD_falso` como destino.

## Licença

Uso livre e pessoal.
