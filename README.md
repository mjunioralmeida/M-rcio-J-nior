# Howdy no Zorin OS 18

Este repositório documenta uma configuração de reconhecimento facial com **Howdy** no Zorin OS 18. É um guia de referência, não um instalador: os nós de câmera e os ajustes podem variar entre computadores.

## Configuração documentada

- **Sistema:** Zorin OS 18, baseado no Ubuntu 24.04 (Noble), com GNOME 46.
- **Computador de referência:** Dell Pro Max 16 MC16250.
- **Câmera IR usada:** `/dev/video2`, identificada pelo formato `GREY`.
- **Ajuste de luminosidade:** `dark_threshold = 90`, baseado na leitura observada nesse fluxo de câmera.
- **Autenticação:** Howdy integrado ao PAM; a senha continua disponível como alternativa.

## Comece pelo guia

Leia [docs/howdy-zorin-18.md](docs/howdy-zorin-18.md) antes de aplicar os comandos em outro computador. O guia explica a instalação compatível com Noble, a falha PEP 668 do pacote antigo, a identificação da câmera IR, o teste e o cadastro facial.

Na configuração documentada, o último passo ainda é cadastrar o rosto:

```bash
sudo howdy add
```

Depois que o cadastro confirmar sucesso, teste o bloqueio com **Super+L**. Se o teste não reconhecer seu rosto, a senha deve continuar habilitada.

## Código aberto e privacidade

O Howdy é um projeto de código aberto. O pacote Noble usado no guia vem de um PPA comunitário de terceiros, não do repositório oficial do Zorin/Ubuntu; a origem está descrita no guia. O conteúdo deste repositório fica limitado a documentação sobre Zorin/Howdy.

O modelo facial e as imagens da câmera são locais e não fazem parte deste repositório. Não publique arquivos biométricos, imagens, capturas de tela com rostos ou logs de autenticação.
