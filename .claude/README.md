# Claude Skills

Este diretório mantém as skills do projeto no formato adotado para o Claude.

## Padrão do repositório

- Cada skill fica em `.claude/skills/<skill-slug>/SKILL.md`
- O mesmo `<skill-slug>` deve existir em `.gemini/skills/<skill-slug>/SKILL.md`
- O conteúdo das skills de Claude e Gemini deve permanecer sincronizado

## Empacotamento

Para subir uma skill no Claude, compacte a pasta da skill inteira, mantendo a pasta da skill como raiz do arquivo compactado.

Exemplo:

```text
firebird-database.zip
└── firebird-database/
    └── SKILL.md
```

## Observação

Embora parte da documentação pública do Claude cite `Skill.md`, a especificação aberta e os exemplos oficiais do Anthropic usam `SKILL.md`. O projeto adota `SKILL.md` para manter consistência entre Claude, Gemini e o ecossistema Agent Skills.