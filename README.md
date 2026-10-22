# PDD Toolkit

[![build](https://github.com/lsbeloti/pdd-toolkit/actions/workflows/build.yml/badge.svg)](https://github.com/lsbeloti/pdd-toolkit/actions/workflows/build.yml)
[![licença: MIT](https://img.shields.io/badge/licen%C3%A7a-MIT-blue.svg)](LICENSE)

Interface desktop (PyQt5) para trabalhar com curvas de **PDD** (*Percent Depth Dose*) em física
médica: ajuste de curvas, correção de Mayneord, superfícies A/B, kermas (Hopfield/Fourier),
reconstrução de PDD e comparação entre curvas.

<details>
<summary><b>English summary</b></summary>

**PDD Toolkit** is a desktop GUI (PyQt5) for working with Percent Depth Dose (PDD) curves in
medical physics: curve fitting, Mayneord correction, A/B surfaces, kerma derivation
(Hopfield/Fourier), direct and inverse PDD reconstruction, and curve comparison. The interface
is in English; the documentation and installer messages are in Brazilian Portuguese. No sample data is included — see
[Formato dos dados de entrada](#formato-dos-dados-de-entrada) for the expected CSV layout.
Linux and Windows binaries are available on the
[Releases](https://github.com/lsbeloti/pdd-toolkit/releases) page. Research and educational
use only; this is not a certified medical device.

</details>

## Funcionalidades

O programa tem três abas:

| Aba | O que faz |
|-----|-----------|
| **Direct** | Carrega uma curva base (CSV), ajusta os parâmetros (A, B), aplica a correção de Mayneord, gera superfícies A/B, reconstrói um ponto e exibe a superfície 3D. |
| **Inverse** | Kermas (Hopfield/Fourier), coeficientes, modelo generalizado (A, τ, β) e reconstrução de PDD avançada ou simples. |
| **Comparison** | Curvas de referência (de arquivo ou digitadas), comparação e erro em relação à referência. |

> **Aviso.** Este software é destinado a pesquisa.

> **Este repositório não inclui dados de exemplo.** As curvas vêm do seu próprio arquivo; o
> formato está descrito em [Formato dos dados de entrada](#formato-dos-dados-de-entrada).

---

## Instalação

### Linux (executável pronto, sem Python)

```bash
curl -fsSL https://raw.githubusercontent.com/lsbeloti/pdd-toolkit/main/install.sh | bash
```

Para abrir o programa:

```bash
pdd-toolkit
```

O instalador baixa o executável do último release, instala em `~/.local/opt/pdd-toolkit`, cria
o comando `pdd-toolkit` em `~/.local/bin` e adiciona um atalho ao menu de aplicativos. Depois
do download, não são necessários Python nem conexão com a internet.

Se preferir inspecionar antes de executar, baixe o [`install.sh`](install.sh), leia e rode
localmente, ou baixe `pdd-toolkit-linux-x86_64.tar.gz` diretamente na página de
[Releases](https://github.com/lsbeloti/pdd-toolkit/releases) e extraia onde quiser.

Requisitos: Linux x86_64 com glibc 2.35 ou mais nova (Ubuntu 22.04 ou posterior, ou
equivalente). Se `pdd-toolkit` não for encontrado, adicione ao `~/.bashrc`:

```bash
export PATH="$HOME/.local/bin:$PATH"
```

### Windows (executável pronto, sem Python)

Baixe `pdd-toolkit-windows-x86_64.zip` na página de
[Releases](https://github.com/lsbeloti/pdd-toolkit/releases), extraia e execute
`PDD_Toolkit\PDD_Toolkit.exe`.

### A partir do código-fonte

Requer Python 3 e o módulo `venv`.

```bash
sudo apt install python3 python3-venv      # Debian/Ubuntu, apenas na primeira vez
git clone https://github.com/lsbeloti/pdd-toolkit.git
cd pdd-toolkit
bash setup_venv.sh          # cria .venv/ e instala as dependências (uma vez)
bash abrir_pdd_toolkit.sh   # abre o programa
```

No Windows (PowerShell ou Prompt de Comando):

```bat
py -m venv .venv
.venv\Scripts\pip install -r requirements.txt
abrir_pdd_toolkit.bat
```

---

## Formato dos dados de entrada

A primeira coluna é a profundidade (cm); cada coluna seguinte é uma curva de PDD, e o
cabeçalho identifica o parâmetro daquela curva:

| Cabeçalho | Parâmetro reconhecido |
|-----------|------------------------|
| `6x6`, `10x10`, `20x20` | tamanho de campo (cm) |
| `SSD 100`, `ssd=90` | SSD (cm) |
| `6MV`, `10MeV` | energia (MV) |

- Separador de campos `;` com decimal `,` ou `.` funciona. Separador `,` só funciona com
  decimal `.`, nunca use `,` nos dois papéis, o arquivo não é lido corretamente.
- Valores relativos (%) ou absolutos (cGy) são aceitos: o ajuste normaliza cada curva pelo seu
  máximo e informa o fator de escala.
- A entrada é apenas CSV; as tabelas geradas pelo programa é que são gravadas em CSV e em um
  `.xlsx` irmão.

Exemplo (valores **ilustrativos**, não medidos):

```csv
x (cm);6x6;10x10;20x20
0;42.5;47.1;52.0
1;96.2;96.8;97.4
1.5;100.0;100.0;100.0
2;99.1;99.2;99.3
5;88.4;89.7;91.2
10;67.3;69.8;72.5
20;38.6;41.2;44.9
```

As saídas (figuras e planilhas) são gravadas em `output_direto/` e `output_indireto/`, ao lado
do programa. Se essa pasta for somente leitura, o programa usa `~/PDD_Toolkit`.

---

## Desenvolvimento

### Autoteste

```bash
python main_pyqt.py --selftest    # termina com SELFTEST OK
```

Em máquinas sem ambiente gráfico (servidores, CI), use
`QT_QPA_PLATFORM=offscreen python main_pyqt.py --selftest`.

O autoteste usa uma curva sintética gerada em tempo de execução e valida imports, leitura de
CSV, ajuste, kernel, matplotlib e openpyxl, sem depender de arquivos de dados.

### Compilar o executável

```bash
pip install pyinstaller
pyinstaller --noconfirm --clean pdd_toolkit.spec   # resultado em dist/PDD_Toolkit/
```

O `.spec` gera um pacote *onedir* autossuficiente e exclui `tkinter` e `IPython`. Variáveis de
ambiente: `PDD_ONEFILE=1` (arquivo único) e `PDD_CONSOLE=1` (mantém o console, para depuração).

### Releases

Os releases são gerados pelo GitHub Actions (`.github/workflows/build.yml`) quando uma tag
`vX.Y.Z` é enviada:

```bash
git tag -a vX.Y.Z -m "PDD Toolkit X.Y.Z"
git push origin vX.Y.Z
```

O workflow roda o autoteste (Ubuntu 22.04 e Windows), compila os executáveis e anexa
`pdd-toolkit-linux-x86_64.tar.gz` e `pdd-toolkit-windows-x86_64.zip` ao release. O build Linux
usa Ubuntu 22.04 de propósito: o binário só roda em distribuições com glibc igual ou mais nova
que a da máquina de build.

---

## Estrutura

```
main_pyqt.py                        interface PyQt5 (ponto de entrada)
common/                             utilitários de cálculo
  io_utils.py                       leitura/escrita de CSV e Excel
  mayneord.py                       correção de Mayneord
  kernel.py                         matriz de kernel
direct/                             aba Direct
  fitting.py                        ajuste (A, B) e modelo de PDD
  surfaces.py                       superfícies A/B
  reconstruction.py                 reconstrução de ponto
  comparison.py                     comparação com referência
  plotting.py                       gráficos (backend Agg)
indirect/                           aba Inverse
  hopfield.py, kerma_batch.py, kerma_variation.py
  reconstruction.py                 reconstrução de PDD indireta
  plotting.py                       gráficos (backend Agg)
setup_venv.sh                       cria o ambiente virtual (Linux)
abrir_pdd_toolkit.sh / .bat         abrem o programa a partir do código-fonte
install.sh                          instala o executável pronto (Linux)
pdd_toolkit.spec                    configuração do PyInstaller
```

---

## Problemas e sugestões

Abra uma [issue](https://github.com/lsbeloti/pdd-toolkit/issues) descrevendo o que aconteceu,
a versão usada e o sistema operacional. **Não anexe dados de pacientes nem dados
institucionais**, se precisar reproduzir um problema, use uma curva sintética.

## Licença

MIT: veja [LICENSE](LICENSE). Em resumo: qualquer pessoa pode usar, copiar, modificar e
redistribuir o programa, desde que mantenha o aviso de copyright; o software é fornecido sem
garantias.
