---
name: pr
description: Revisa la rama con el agente revisor-pr y abre una pull request en GitHub con la plantilla del equipo.
disable-model-invocation: true
allowed-tools:
  - Bash(git status *)
  - Bash(git branch *)
  - Bash(git log *)
  - Bash(git push *)
  - Bash(gh pr create *)
---

# Abrir una pull request

## Situación actual

- Rama: !`git branch --show-current`
- Cambios sin commit: !`git status --short`
- Commits de la rama respecto a main: !`git log --oneline main..HEAD`

## Pasos

1. Comprueba que se puede abrir la PR:
   - Si estás en `main` o la rama no tiene commits nuevos, para y explícaselo al usuario.
   - Si hay cambios sin commit, pregunta al usuario si quiere hacer antes el commit (skill `commit`).
2. Lanza el agente `revisor-pr` para revisar la rama.
3. Si el resultado es BLOQUEANTE, enseña los problemas al usuario y para.
4. Enseña al usuario el título, la descripción y los avisos que propone el revisor. Pídele confirmación y aplica los cambios que te diga.
5. Sube la rama con `git push -u origin HEAD`.
6. Crea la PR con `gh pr create --base main --title "<título>" --body "<descripción>"`.
7. Devuelve el enlace de la PR.
