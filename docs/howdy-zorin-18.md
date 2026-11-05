# Desbloqueio facial no Zorin OS 18 com Howdy

Este documento registra a configuração feita neste computador Dell Pro Max 16 MC16250 com Zorin OS 18 (base Ubuntu 24.04/Noble), GNOME 46 e câmera integrada RGB/IR. Os comandos de câmera são específicos desta máquina; identifique os dispositivos de vídeo no seu próprio computador antes de copiar o caminho.

## Componentes e origem

- [Howdy](https://github.com/boltgolt/howdy): autenticador facial de código aberto que usa câmera IR e PAM.
- Pacote instalado: `howdy 2.6.1+beta+slimbook5`, do PPA comunitário `ppa:ubuntuhandbook1/howdy` para Ubuntu Noble.
- Dependências de visão computacional: `python3-dlib`, `python3-opencv`, `python3-numpy` e os dados de modelo do dlib, gerenciados por pacotes APT.
- Python do Zorin/Ubuntu é gerenciado pelo sistema. O instalador antigo do PPA `ppa:boltgolt/howdy` tentou instalar/atualizar componentes com `pip` global e falhou pela política [PEP 668](https://peps.python.org/pep-0668/). Não contorne esse bloqueio com `--break-system-packages`.

O PPA `ubuntuhandbook1/howdy` é de terceiros, não faz parte dos repositórios oficiais do Zorin/Ubuntu. Confira o pacote e sua origem antes de instalar em outra máquina: [índice de pacotes Noble do PPA](https://ppa.launchpadcontent.net/ubuntuhandbook1/howdy/ubuntu/dists/noble/main/binary-amd64/Packages.gz). O projeto Howdy original é MIT, mas a origem do pacote continua sendo importante.

## Instalação no Ubuntu Noble / Zorin OS 18

Em uma instalação nova, use o pacote compatível com Noble:

```bash
sudo add-apt-repository ppa:ubuntuhandbook1/howdy
sudo apt update
sudo apt install howdy
```

No computador documentado, o pacote original já havia ficado parcialmente configurado após o erro de PEP 668. Adicionar o PPA de Noble e instalar `howdy` substituiu-o pela versão recompilada e trouxe as dependências necessárias pelo APT.

Se o PPA original `ppa:boltgolt/howdy` tiver sido adicionado durante uma tentativa anterior, remova-o depois de confirmar que `howdy` está instalado a partir do pacote compatível:

```bash
sudo add-apt-repository --remove ppa:boltgolt/howdy
sudo apt update
```

## Identificar a câmera IR

Os dispositivos de vídeo podem representar a câmera colorida, a câmera IR e interfaces auxiliares. Não presuma que `/dev/video0` é a câmera de reconhecimento.

```bash
sudo apt install v4l-utils
v4l2-ctl --list-devices
v4l2-ctl --device=/dev/video2 --list-formats-ext
```

Neste Dell, `/dev/video2` foi identificado pelo formato `GREY` e forneceu o fluxo infravermelho. O teste do Howdy mostrou a imagem do rosto e o indicador `SCAN FRAME` verde. Use o dispositivo IR correspondente ao seu hardware.

Configure o caminho:

```bash
sudo howdy set device_path /dev/video2
```

## Ajustar o filtro de quadros escuros

O valor inicial era `dark_threshold = 60`. No fluxo IR deste computador, o Howdy informou cerca de 81–84% de pixels no intervalo escuro e descartou todos os quadros. O teste visual mostrava o rosto, mas marcava `DARK FRAME`.

O limite foi aumentado para 90, acima da medição observada:

```bash
sudo howdy set dark_threshold 90
```

Esse valor é específico deste fluxo e não deve ser copiado sem testar. O limite decide quais quadros escuros são ignorados; não é a configuração de certeza usada para comparar rostos. Se outra câmera produzir outra leitura, ajuste com base no resultado do teste e preserve a senha como método alternativo.

## Testar e cadastrar o rosto

Abra o teste:

```bash
sudo howdy test
```

O teste mantém a janela aberta e o iluminador IR pode piscar enquanto a câmera está sendo usada. `SCAN FRAME` verde indica que os quadros estão passando pelo filtro de luminosidade. Pressione **Ctrl+C no terminal** para encerrar o teste.

Cadastre o modelo facial:

```bash
sudo howdy add
```

Informe um rótulo, olhe para a câmera e aguarde a confirmação de que o modelo foi salvo. Confira os modelos:

```bash
sudo howdy list
```

No computador documentado, a configuração da câmera e o teste estavam concluídos, mas o cadastro ainda estava pendente na última verificação. O próximo passo é `sudo howdy add`; o reconhecimento pela tela de bloqueio não deve ser considerado pronto até esse cadastro terminar.

## PAM e tela de bloqueio

O pacote ativou o módulo Howdy no fluxo PAM comum (`/etc/pam.d/common-auth`), que é usado pelos serviços de autenticação. Não edite nem remova manualmente a configuração PAM sem manter um método de senha funcional. Depois do cadastro, bloqueie com **Super+L** e teste o reconhecimento. A senha deve continuar disponível como alternativa.

## Privacidade e o que não publicar

O modelo facial é dado biométrico e fica localmente em `/usr/share/dlib/<usuário>.dat`. **Não envie nem comite esse arquivo, imagens da câmera, logs de autenticação ou capturas de tela com o rosto para um repositório GitHub.** Compartilhe somente este procedimento e as configurações não biométricas.

O software descrito é de código aberto; o PPA é comunitário e não oficial do Zorin. Isso não significa que todo firmware do computador seja aberto.

## Referências

- [Projeto Howdy e instruções upstream](https://github.com/boltgolt/howdy)
- [PPA comunitário para Noble](https://launchpad.net/~ubuntuhandbook1/+archive/ubuntu/howdy)
- [Índice do pacote Howdy do PPA para Noble/amd64](https://ppa.launchpadcontent.net/ubuntuhandbook1/howdy/ubuntu/dists/noble/main/binary-amd64/Packages.gz)
- [PEP 668: ambientes Python gerenciados externamente](https://peps.python.org/pep-0668/)
