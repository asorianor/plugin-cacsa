# flujo-git

Plugin de Claude Code para que Claude trabaje con git como el equipo: ramas con nombre según la convención, commits con [Conventional Commits](https://www.conventionalcommits.org/es/v1.0.0/) y pull requests en GitHub con plantilla.

## Qué incluye

| Pieza | Tipo | Qué hace |
|---|---|---|
| `convenciones` | Skill automática | Contiene las reglas del equipo. Claude la carga solo antes de crear una rama, hacer un commit o abrir una PR |
| `/flujo-git:commit [pista]` | Skill manual | Si estás en `main`, crea antes una rama. Hace un commit por cada cambio lógico y no hace push |
| `/flujo-git:pr` | Skill manual | Revisa la rama con el agente, te enseña la propuesta y, cuando confirmas, abre la PR con `gh` |
| `revisor-pr` | Subagente (Haiku, solo lectura) | Comprueba que la rama cumple las convenciones y propone el título y la descripción de la PR |
| `proteger-ramas.sh` | Hook `PreToolUse` | Bloquea `git commit` en `main` y `master` |
| `validar-commit.sh` | Hook `PostToolUse` | Si el último commit no sigue Conventional Commits, avisa a Claude para que lo corrija con `--amend` |

Las skills le dicen a Claude cómo trabajar y los hooks lo comprueban: `proteger-ramas` impide el commit y `validar-commit` avisa para que se corrija. El revisor es la última capa: si queda algún commit incorrecto, `/flujo-git:pr` no abre la PR.

## Requisitos

- Git y la CLI de GitHub (`gh`) con la sesión iniciada (`gh auth login`).
- En Windows, Git for Windows: los hooks son scripts bash y se ejecutan con Git Bash.

## Instalación

```
/plugin marketplace add <organización>/<repo-del-marketplace>
/plugin install flujo-git@equipo
```

Para probarlo sin instalarlo: `claude --plugin-dir ./plugins/flujo-git`.

## Personalizar

| Quiero cambiar… | Archivo |
|---|---|
| Formato de las ramas, tipos de commit, idioma o plantilla de la PR | `skills/convenciones/SKILL.md` |
| Tipos de commit que acepta el hook (deben coincidir con los de las convenciones) | `hooks/validar-commit.sh`, variable `TIPOS` |
| Ramas en las que no se permite hacer commit | `hooks/proteger-ramas.sh`, variable `RAMAS_PROTEGIDAS` |
| Qué revisa el agente antes de la PR, o con qué modelo | `agents/revisor-pr.md` |
| Rama base distinta de `main` | Buscar `main` en `skills/convenciones/SKILL.md`, `skills/pr/SKILL.md` y `agents/revisor-pr.md` |

Después de cambiar algo:

- Si instalaste el plugin desde una carpeta local, ejecuta `/reload-plugins`.
- Si el marketplace está en GitHub, sube `version` en `.claude-plugin/plugin.json` y haz push. Cada persona lo actualiza con `claude plugin marketplace update equipo` y `claude plugin update flujo-git@equipo`, y reinicia Claude Code.

Comprueba que todo sigue bien con `claude plugin validate .`.
