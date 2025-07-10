<script setup lang="ts">
// ...imports remain unchanged...

// --- SNIP: keep all your current imports ---

interface FrameStyle {
  textColor: string
  backgroundColor: string
  borderColor: string
  borderWidth: string
  borderRadius: string
  padding: string
}

const props = defineProps<{
  initialData?: string
}>()

const mainContentContainer = ref<HTMLElement | null>(null)
const isLarge = useMediaQuery('(min-width: 768px)')
const isLikelyMobileDevice = computed(() => {
  return typeof navigator !== 'undefined' && navigator.maxTouchPoints > 0
})

//#region /** locale */
const { t } = useI18n()
//#endregion

//#region /* QR code style settings */
const data = ref(props.initialData || "https://bit.ly/phillyoshp")
const debouncedData = ref(data.value)
let dataDebounceTimer: ReturnType<typeof setTimeout>
watch(
  data,
  (newVal) => {
    clearTimeout(dataDebounceTimer)
    dataDebounceTimer = setTimeout(() => {
      debouncedData.value = newVal
    }, 500)
  },
  { immediate: true }
)

// ---- Hardcoded config values ----
const image = ref("data:image/svg+xml;base64,PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0iVVRGLTgiIHN0YW5kYWxvbmU9Im5vIj8+CjxzdmcKICAgdmVyc2lvbj0iMS4xIgogICBpZD0iTGF5ZXJfMSIKICAgeD0iMHB4IgogICB5PSIwcHgiCiAgIHdpZHRoPSIxMDAlIgogICB2aWV3Qm94PSIwIDAgNjU4IDM2NyIKICAgZW5hYmxlLWJhY2tncm91bmQ9Im5ldyAwIDAgNjU4IDM2NyIKICAgeG1sOnNwYWNlPSJwcmVzZXJ2ZSIKICAgc29kaXBvZGk6ZG9jbmFtZT0iUFJPLUFDVF9wdXJwbGUuc3ZnIgogICBpbmtzY2FwZTp2ZXJzaW9uPSIxLjQgKDg2YThhZDcsIDIwMjQtMTAtMTEpIgogICB4bWxuczppbmtzY2FwZT0iaHR0cDovL3d3dy5pbmtzY2FwZS5vcmcvbmFtZXNwYWNlcy9pbmtzY2FwZSIKICAgeG1sbnM6c29kaXBvZGk9Imh0dHA6Ly9zb2RpcG9kaS5zb3VyY2Vmb3JnZS5uZXQvRFREL3NvZGlwb2RpLTAuZHRkIgogICB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciCiAgIHhtbG5zOnN2Zz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciPjxkZWZzCiAgIGlkPSJkZWZzNzYiIC8+PHNvZGlwb2RpOm5hbWVkdmlldwogICBpZD0ibmFtZWR2aWV3NzYiCiAgIHBhZ2Vjb2xvcj0iI2ZmZmZmZiIKICAgYm9yZGVyY29sb3I9IiMxMTExMTEiCiAgIGJvcmRlcm9wYWNpdHk9IjEiCiAgIGlua3NjYXBlOnNob3dwYWdlc2hhZG93PSIwIgogICBpbmtzY2FwZTpwYWdlb3BhY2l0eT0iMCIKICAgaW5rc2NhcGU6cGFnZWNoZWNrZXJib2FyZD0iMSIKICAgaW5rc2NhcGU6ZGVza2NvbG9yPSIjZDFkMWQxIgogICBpbmtzY2FwZTp6b29tPSIxLjYzOTYzOTkiCiAgIGlua3NjYXBlOmN4PSIyNzIuMzE1ODkiCiAgIGlua3NjYXBlOmN5PSIyNTUuNTQzOTEiCiAgIGlua3NjYXBlOndpbmRvdy13aWR0aD0iMTkyMCIKICAgaW5rc2NhcGU6d2luZG93LWhlaWdodD0iMTExNSIKICAgaW5rc2NhcGU6d2luZG93LXg9Ii05IgogICBpbmtzY2FwZTp3aW5kb3cteT0iLTkiCiAgIGlua3NjYXBlOndpbmRvdy1tYXhpbWl6ZWQ9IjEiCiAgIGlua3NjYXBlOmN1cnJlbnQtbGF5ZXI9IkxheWVyXzEiIC8+CgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgo8ZwogICBpZD0iZzIiCiAgIGlua3NjYXBlOmxhYmVsPSJQdXJwbGUgT3ZhbCBXaGl0ZSBCb3JkZXIgQmxhY2sgQm9yZGVyIgogICB0cmFuc2Zvcm09Im1hdHJpeCgzLjc5NDQwMzEsMCwwLDMuODkyNzM2NiwtMzYuMTYwMDQ5LDIuOTEyMjk4NCkiPjxlbGxpcHNlCiAgICAgc3R5bGU9Im9wYWNpdHk6MTtmaWxsOiM1NTI5OGE7ZmlsbC1vcGFjaXR5OjE7c3Ryb2tlOiNmZmZmZmY7c3Ryb2tlLXdpZHRoOjU7c3Ryb2tlLWxpbmVqb2luOmJldmVsO3N0cm9rZS1taXRlcmxpbWl0OjM0LjU7c3Ryb2tlLWRhc2hhcnJheTpub25lO3N0cm9rZS1vcGFjaXR5OjE7cGFpbnQtb3JkZXI6ZmlsbCBtYXJrZXJzIHN0cm9rZSIKICAgICBpZD0icGF0aDI2IgogICAgIGN4PSI5Ni44NjkwMDMiCiAgICAgY3k9IjQ1LjE4NDUwMiIKICAgICByeD0iNTcuMzMzODk3IgogICAgIHJ5PSIyOC43MjMzOTYiCiAgICAgaW5rc2NhcGU6bGFiZWw9InB1cnBsZSBvdmFsIHdoaXRlIGJvcmRlciIgLz48ZWxsaXBzZQogICAgIHN0eWxlPSJvcGFjaXR5OjE7ZmlsbDojNTU1NTIyO2ZpbGwtb3BhY2l0eTowO3N0cm9rZTojNTUyOThhO3N0cm9rZS13aWR0aDoyLjA3NjI0O3N0cm9rZS1saW5lam9pbjpiZXZlbDtzdHJva2UtbWl0ZXJsaW1pdDozNC41O3N0cm9rZS1kYXNoYXJyYXk6bm9uZTtzdHJva2Utb3BhY2l0eToxO3BhaW50LW9yZGVyOmZpbGwgbWFya2VycyBzdHJva2UiCiAgICAgaWQ9InBhdGgyNi01IgogICAgIGN4PSI5Ni44NjkwMDMiCiAgICAgY3k9IjQ1LjE4NDQ5OCIKICAgICByeD0iNTguNzk1ODc5IgogICAgIHJ5PSIzMC4xODUzNzciCiAgICAgaW5rc2NhcGU6bGFiZWw9InB1cnBsZSBib3JkZXIiIC8+PC9nPjxnCiAgIGlkPSJnMjktMyIKICAgaW5rc2NhcGU6bGFiZWw9IlBSTy1BQ1QiCiAgIHRyYW5zZm9ybT0ibWF0cml4KDMuNzgyNTU0MiwwLDAsMy44Njg5MTM4LC0zNS4wMTM1NTYsMy45ODA2MzI2KSI+PGcKICAgICBpZD0iZzI3LTMiCiAgICAgaW5rc2NhcGU6bGFiZWw9IkFDVCI+PHBhdGgKICAgICAgIGZpbGw9IiNmN2Y2ZmEiCiAgICAgICBvcGFjaXR5PSIxIgogICAgICAgc3Ryb2tlPSJub25lIgogICAgICAgZD0ibSAxMzcuNDI3ODcsNDQuOTY3ODI3IGMgLTAuMDk3LDEuOTU1MTgzIC0wLjIyODMsMy43ODk1OTEgLTAuMjY2MzEsNS42MjU5MzEgLTAuMDE2LDAuNzc0NDMgMC4yNTA2NywxLjU1NTAzIDAuMjMxOTIsMi4zMjkwMiAtMC4wMTkyLDAuNzk0MDYgLTAuMjUwNDIsMS43MjQ1OCAtMS4yNDM5NSwxLjY3MjIgLTAuODg1NTgsLTAuMDQ2NyAtMS40NTE4OCwtMC42NjYyMSAtMS4zODIyMSwtMS43MTI3NiAwLjI4MjY0LC00LjI0NjA5IDAuNTM4OTksLTguNDkzOTk1IDAuNzg2NzcsLTEyLjc0MjI5NiAwLjA0NTYsLTAuNzgyNTM0IDAuMDA3LC0xLjU3MDAxMSAwLjAwNywtMi4zMjcxNCAtMS41Mjk2MywwLjA2ODQyIC0yLjk2MTg1LDAuMTU1NDM1IC00LjM5NTI2LDAuMTg1MTE2IC0wLjU1OTcyLDAuMDExNTkgLTEuMTcxMDYsMC4wMDk2IC0xLjY2NzY1LC0wLjIwMTIyNCAtMC4zODg5LC0wLjE2NTA4MSAtMC42MzcyLC0wLjY2MTQ0MiAtMC45NDc5NywtMS4wMTA2MjEgMC40MTg0OSwtMC4zNjc0IDAuODIwMTQsLTEuMDMxNTcxIDEuMjU4MDYsLTEuMDU2NDI4IDQuNTE5MDMsLTAuMjU2NTIyIDkuMDQzNTMsLTAuNDIxMzE1IDEzLjU2NzgzLC0wLjU3MjE1MSAwLjQwOTg5LC0wLjAxMzY1IDAuOTY4OTIsMC4xMjc2NTEgMS4yMTA1MywwLjQwOTYzNiAwLjM1MjM1LDAuNDExMjAyIDAuNDc5NTMsMS4wMTUzMzggMC43MDI2OSwxLjUzNzIyNiAtMC41NTA1NywwLjExNzExIC0xLjEwMjM4LDAuMzM3NzI1IC0xLjY1MTQ5LDAuMzMxMjEzIC0xLjQzNzQzLC0wLjAxNzA0IC0yLjg3NTcsLTAuMTA3NTA1IC00LjMwOTY3LC0wLjIxOTQ0NSAtMC44OTA1LC0wLjA2OTUyIC0xLjA3MzI3LDAuMzI4ODg4IC0xLjE0MjE5LDEuMTUxMTgxIC0wLjE4MTUzLDIuMTY2MDM4IC0wLjQ5MzkzLDQuMzIxMTA5IC0wLjc1NzYyLDYuNjAwNTQyIHoiCiAgICAgICBpZD0icGF0aDcxLTQiCiAgICAgICBzdHlsZT0ic3Ryb2tlLXdpZHRoOjAuMjY0NTgzIgogICAgICAgaW5rc2NhcGU6bGFiZWw9IlQiIC8+PHBhdGgKICAgICAgIGZpbGw9IiNmOGY3ZmEiCiAgICAgICBvcGFjaXR5PSIxIgogICAgICAgc3Ryb2tlPSJub25lIgogICAgICAgZD0ibSAxMTYuNzg3NjEsNDcuMjc4NDg4IGMgMC4zODg1OCwxLjQ2ODg1IC0wLjEzNDk5LDMuMDY2MSAxLjAyNzc1LDQuMTk2NDcgMC41NDEwMywwLjUyNTk1IDEuMTM2ODEsMS4wOTcxNSAxLjgxODY1LDEuMzU3OCAyLjExMTk1LDAuODA3MzcgNC42NjE1NCwtMC4xMDk0MSA1Ljk2NTQ0LC0yLjAwNjEgMC42MjE2NSwtMC45MDQyNiAxLjQzMTM3LC0wLjgxMTMxIDIuMTY1MDksLTAuMjU4OTMgMC43NTA3LDAuNTY1MTcgMC43ODk0NywxLjE5MTA0IDAuMDM4NywxLjk1MjE5IC0yLjI4NDY1LDIuMzE2MTEgLTUuMDc0MDIsMy4xNTI2OCAtOC4yMTg5NiwyLjkzMTQgLTIuMzQ1NzMsLTAuMTY1MDQgLTQuNDkxNjYsLTIuOTI0NCAtNC45NDc2MSwtNS43MTc2NCAtMC43MDkxNywtNC4zNDQ2MTYgMC41MzQxOSwtOC4yMzQ2MDUgMi44MDk2MywtMTEuNzM4NDgyIDAuOTg2OTgsLTEuNTE5ODI3IDIuNzQ0NzYsLTIuNzEzNDQ3IDQuNDAxMiwtMy41NzkzNTIgMi4wMDEwOSwtMS4wNDYwNjUgNC41NjI2LDAuNzAyMTkxIDUuNDg0MTEsMy40MTgxMjMgMC4xNzk5LDAuNTMwMjM2IDAuMzUwNDMsMS4xMTYzMTEgMC4zMDEwNiwxLjY1NjEyOCAtMC4wNDQ5LDAuNDkwNDI5IC0wLjI3OTY4LDEuMjEzOTM3IC0wLjYzNTQ4LDEuMzcxMDM5IC0wLjY1MjM0LDAuMjg4MDMzIC0xLjMwMTM2LDAuMDczMyAtMS42NTY0NSwtMC43NzUxMDUgLTAuNDIzODMsLTEuMDEyNjQ1IC0wLjk2NiwtMS45ODk3NTQgLTEuNTY5MjEsLTIuOTA4NTM4IC0wLjQyMDI1LC0wLjY0MDExNSAtMS4wMjM4NSwtMC42NDc1MzEgLTEuNjk2NzYsLTAuMTcxMDUxIC0yLjkwMDk0LDIuMDU0MTMzIC00LjUyMTc2LDQuODYzMjk2IC01LjA0NzE2LDguMzQzMTYyIC0wLjA5MDksMC42MDIwMjUgLTAuMTYwNTIsMS4yMDcyNjIgLTAuMjM5OTcsMS45Mjg4ODYgeiIKICAgICAgIGlkPSJwYXRoNzAtMSIKICAgICAgIHN0eWxlPSJzdHJva2Utd2lkdGg6MC4yNjQ1ODMiCiAgICAgICBpbmtzY2FwZTpsYWJlbD0iQyIgLz48cGF0aAogICAgICAgZmlsbD0iI2Y4ZjdmYSIKICAgICAgIG9wYWNpdHk9IjEiCiAgICAgICBzdHJva2U9Im5vbmUiCiAgICAgICBkPSJtIDEwMi43ODUsNDUuOTMyODUgYyAxLjE5NDI1LC0zLjI5NzgwNyAyLjM3NTk5LC02LjQ5MjA5MiAzLjUzMTksLTkuNjk1Njk0IDAuMTI3ODQsLTAuMzU0MzA4IDAuMTg2NSwtMC43NzEzNTggMC4xNDEzMiwtMS4xNDI3NzcgLTAuMTA3NzUsLTAuODg1ODg0IDAuMjQzOTIsLTEuNTkyNjE3IDEuMDc1MzIsLTEuNjE3NTE1IDAuNTUzODQsLTAuMDE2NTkgMS4zMDI2NCwwLjQyNDQwNSAxLjY0NTUxLDAuODkzNTExIDAuNDY1NTYsMC42MzY5NTggMC43MjU3LDEuNDc0NzM4IDAuODk5MjcsMi4yNjQ3NjIgMC40OTI3MiwyLjI0MjU4NSAwLjg5NjcsNC41MDQ2NjQgMS4zNjM1OCw2LjkwMjY5NiAwLjA5MTYsMC4wMzM4NiAwLjM2MywwLjE0NDkwMiAwLjY0MTEzLDAuMjM1MDQzIDEuMTg4NDQsMC4zODUxNzIgMS40NzEyNSwxLjIxMTUyMiAwLjQ5NDkxLDEuOTMzMzk4IC0wLjk4OTExLDAuNzMxMzExIC0wLjgwMzAzLDEuNTI3NDI0IC0wLjcwNzU1LDIuNDk1MTY0IDAuMjE5MjksMi4yMjI0OCAwLjM2Nzc0LDQuNDU1NzcgMC40MTU3Nyw2LjY4NzQ2IDAuMDA4LDAuMzY2MTEgLTAuNzcyNDIsMS4xMTYyMSAtMC45NjIwMSwxLjA0OTExIC0wLjUxOTkyLC0wLjE4NDAxIC0xLjIwNzk2LC0wLjY0NzUyIC0xLjMwNjMxLC0xLjExMTM0IC0wLjIwNjA5LC0wLjk3MTgyIC0wLjA0NDcsLTIuMDE2MzEgLTAuMDkyNiwtMy4wMjgyMiAtMC4wNjgyLC0xLjQ0MjEzIC0wLjE1NDA0LC0yLjg4NTgxIC0wLjMyMTA2LC00LjMxODQ5IC0wLjAyNzYsLTAuMjM2NzIgLTAuNTMwMDIsLTAuNjE4NTM3IC0wLjc3NTUzLC0wLjU5MTYxMSAtMC43MjIwOSwwLjA3OTE5IC0xLjQyMzk1LDAuMzM1ODAxIC0yLjEzNjIyLDAuNTEyOTQxIC0xLjY0OTg1LDAuNDEwMjkgLTIuODc5NjIsMS4yMzQ4MiAtMy4zMDA3NywzLjAyNTYzIC0wLjQyMzc5LDEuODAyMDMgLTAuOTMwNCwzLjU4NTUzIC0xLjQ0MzUzLDUuMzY0OCAtMC4xMDM3OSwwLjM1OTg1IC0wLjQ1MjA4LDAuOTY0MzEgLTAuNTk2MzgsMC45Mzg4NSAtMC40NTA0OSwtMC4wNzk1IC0xLjA3NTM0LC0wLjMxMTM0IC0xLjIyODM5LC0wLjY2MTAzIC0wLjIxNDM2NywtMC40ODk3OCAtMC4xODYyNjcsLTEuMTY5OTYgLTAuMDQ3MiwtMS43MTM3NSAwLjM2MDk0LC0xLjQxMDk5IDAuODM5MjQsLTIuNzkxOTYgMS4xOTA4OSwtMy45MjcyNCAtMC42ODY0NCwtMC40MTkxNSAtMS41MDUzNjcsLTAuNjYzNzMgLTEuNTQ1MTY3LC0xLjAwMDQxIC0wLjA2ODIsLTAuNTc3IDAuMTg2MTgsLTEuMzgyMDggMC42MDIxOTcsLTEuNzgzNTUgMC42ODk1OCwtMC42NjU0NjIgMS42MTQ4LC0xLjA4Njc3NCAyLjQ2MDgzLC0xLjcxMTczOCBtIDMuNTczOSwtMS4wMTk5NjcgYyAwLjg5ODY4LC0wLjI5MTg0MyAxLjc5NzM2LC0wLjU4MzY4NiAyLjgxNzg5LC0wLjkxNTEwMSAtMC4zNjcxNiwtMS44OTc4NTYgLTAuNzMxNjQsLTMuNzgxODc0IC0xLjA5NjEyLC01LjY2NTg5MyAtMS4wMTY3LDIuMDg3NzUzIC0xLjcxOTIzLDQuMjAyNDM4IC0yLjM5Mzc5LDYuMzI2MDA4IC0wLjAxMzMsMC4wNDE4IDAuMzAxMzcsMC4xODc3NjEgMC42NzIwMiwwLjI1NDk4NiB6IgogICAgICAgaWQ9InBhdGg2OC0xIgogICAgICAgc3R5bGU9InN0cm9rZS13aWR0aDowLjI2NDU4MyIKICAgICAgIGlua3NjYXBlOmxhYmVsPSJBIiAvPjwvZz48cGF0aAogICAgIGZpbGw9IiNmNmY0ZjkiCiAgICAgb3BhY2l0eT0iMSIKICAgICBzdHJva2U9Im5vbmUiCiAgICAgZD0ibSA5OS4wMjkxMDMsNDYuMjg5MjcyIGMgMC4wODkzLDEuMTQyNTc2IC0wLjQ1MjY5LDEuNDc0NjI2IC0xLjQxOTU2LDEuNTMyNDg2IC0xLjM4NjMzLDAuMDgzIC0yLjc2NDAxLDAuMzAxODggLTQuMTQ4MDQsMC40MzUzNCAtMC43NzUwNiwwLjA3NDcgLTEuNTk2MDUsMC4yMzkzNSAtMS45OTIyMSwtMC43NTc5NiAtMC40OTA0MSwtMS4yMzQ1OTQgLTAuMDI1NSwtMS43OTUyMzUgMS4yOTA1OCwtMS44MjE5NjEgMS40NDA5NCwtMC4wMjkyNiAyLjg4OTk0LC0wLjE5NDQ2IDQuMzExOTEsLTAuNDM3ODI0IDAuOTU0NTcsLTAuMTYzMzc1IDEuNjQ1MjIsLTAuMDkyODIgMS45NTczMiwxLjA0OTkxOSB6IgogICAgIGlkPSJwYXRoNzItMyIKICAgICBzdHlsZT0ic3Ryb2tlLXdpZHRoOjAuMjY0NTgzIgogICAgIGlua3NjYXBlOmxhYmVsPSItIiAvPjxnCiAgICAgaWQ9ImcyOC04IgogICAgIGlua3NjYXBlOmxhYmVsPSJQUk8iPjxwYXRoCiAgICAgICBmaWxsPSIjZjlmN2ZhIgogICAgICAgb3BhY2l0eT0iMSIKICAgICAgIHN0cm9rZT0ibm9uZSIKICAgICAgIGQ9Im0gODguMzk2NTkzLDQ5LjAyMjMzOCBjIC0xLjQyMjQ2LDEuNzIxMDQgLTIuNTU2OCwzLjY2NzE1IC00LjIxNDIsNC45MjI5MSAtMi4yOTIyLDEuNzM2NzQgLTUuOTQxMTYsMS43MDc1MSAtNy4wNTYwNSwtMS45MTA5MSAtMS40MTU0MSwtNC41OTM3MiAtMC4yMzgzLC04Ljc0OTM3NSAyLjQyNTMsLTEyLjU3Mjk5NiAwLjY3MDU5LC0wLjk2MjYzNiAxLjQ4NDE3LC0xLjgyNTEzMyAyLjIyMzU2LC0yLjc0MDUwNCAwLjkzNTk1LC0xLjE1ODcxMyAyLjI1NjIxLC0xLjY5MTQ4MyAzLjYzMzg5LC0xLjc3MDE1MiAwLjYxNzgxLC0wLjAzNTI4IDEuNDY5NTYsMC41NjQ5MzYgMS44OTE4NywxLjEyMTkgMy4wNTI1MSw0LjAyNTg3NiAzLjY0OTgzLDguMzIwNjY2IDEuMDk1NjMsMTIuOTQ5NzUyIG0gLTMuOTcxNzgsLTExLjAwNjg0NiBjIC0yLjg3MTgxLDIuMTc0NjU4IC0zLjgzNDUsNS4xNjI1MzcgLTMuMzIxNDEsOC41NzYxOTQgMC4yMzc2MiwxLjU4MDkwMiAtMC4yMzk0MywxLjkyMzM1MiAtMS42MTIxLDEuNzI4NjEyIC0wLjA2NTUsLTAuMDA5IC0wLjE0NDExLDAuMDc0NCAtMC4yNzIyMSwwLjE0NTg3IDAsMC43MTM2MyAtMC4wNzU1LDEuNDYzODEgMC4wMTQ5LDIuMTkzNDcgMC4yMjU1NSwxLjgyMTc4IDEuMjgyNTUsMi40NzY5IDIuOTA5MTQsMS42MTA4NiAxLjE0MzE3LC0wLjYwODY2IDIuMzA5NSwtMS40Mjg4IDMuMDczMTUsLTIuNDQ4MzcgMi40NzI3OCwtMy4zMDE0NCAzLjM1MzQ5LC03LjQwMjgxOCAwLjk5NzQxLC0xMS4wNzQ1MDcgLTAuMzc3MzMsLTAuNTg4MDMxIC0wLjY5NjI4LC0xLjI3NzY2NSAtMS43ODg4NSwtMC43MzIxMjkgeiIKICAgICAgIGlkPSJwYXRoNjctNyIKICAgICAgIHN0eWxlPSJzdHJva2Utd2lkdGg6MC4yNjQ1ODMiCiAgICAgICBpbmtzY2FwZTpsYWJlbD0iTyIgLz48cGF0aAogICAgICAgZmlsbD0iI2Y5ZjhmYSIKICAgICAgIG9wYWNpdHk9IjEiCiAgICAgICBzdHJva2U9Im5vbmUiCiAgICAgICBkPSJtIDY0Ljc5NzkzMywzNi40NTc2NTUgYyAxLjE4NTI3LC0yLjAzNTE1OSAzLjUzNzc4LC0zLjA0NDExMSA2LjAzNTkzLC0yLjYzOTA0MiAxLjkxNjE0LDAuMzEwNjk4IDMuOTc2OTIsMi4xODU5NDMgNC4wMTMyOCw0LjI0NTY2MyAwLjAyMjYsMS4yNzg3MjQgLTAuNzQ1OTgsMi42MTIwODEgLTEuMzIyMzksMy44NDQ3MjEgLTAuMzIwNzgsMC42ODU5NzIgLTAuOTU1NzcsMS4yMzM5ODUgLTEuNDg1NTgsMS44MTA3MTEgLTAuNTI4OTUsMC41NzU3NyAtMS4xMDMzNSwxLjEwOTc3OSAtMS43MTUyMSwxLjcxOTI1NCAyLjY2NjQyLDIuNTc1NjY2IDQuMTY0OTEsNS44MTAwOTYgNS42ODExMyw5LjAyMzIyNiAwLjIzNDcyLDAuNDk3NDIgMS4yMDI3OCwxLjEzNDUgMC4wOTk5LDEuNzE2MDUgLTAuODQ1NDUsMC40NDU4IC0xLjcwMjczLC0wLjA2NzMgLTIuMTc1NTIsLTEuMDUxNjMgLTAuNjUwOTQsLTEuMzU1MjkgLTEuMzMwMjIsLTIuNzA1MjkgLTIuMTEyNjMsLTMuOTg3MiAtMS4yMTg5MywtMS45OTcxIC0yLjc5MDA3LC0zLjY3NjI3IC01LjA0Mzk2LC00LjkwNjE0OCAtMC4yNjc2LDIuMjYwMDM4IC0wLjU0ODIsNC40NjMxNTggLTAuNzc3NjcsNi42NzE1NzggLTAuMDY2OSwwLjY0MzY0IC0wLjAxNzgsMS4yMjA2MSAtMC45MzEyLDEuMjY5MzggLTAuOTc0MDUsMC4wNTIgLTEuNDE3NjgsLTAuNDc1MzUgLTEuNDM1NCwtMS4yOTQwMiAtMC4wMjA2LC0wLjk1MDU5IDAuMTg3NiwtMS45MDM3OSAwLjI0OTQ3LC0yLjg1ODkxIDAuMjkxMDUsLTQuNDkzMzg2IDAuNTY1OTcsLTguOTg3ODIxIDAuOTE5ODksLTEzLjU2MzYzMyBtIDYuMDk5MDIsNS4yNDQ4MDEgYyAwLjM3MjQ0LC0wLjYyOTk2OCAwLjkzOTQsLTEuMjIyNDU3IDEuMDY5NzUsLTEuODk5MDY2IDAuMTc2NDIsLTAuOTE1Njc4IDAuMzY0NzUsLTIuMTI1NTQ2IC0wLjA3ODIsLTIuNzk0NzUxIC0wLjY2ODY1LC0xLjAxMDIxNCAtMi4wMzA0OSwtMS4xNDcyNTcgLTMuMjE3NTUsLTAuNzQ2NzM0IC0xLjA3OTkyLDAuMzY0Mzc3IC0xLjQzNTI3LDEuMjgwNzkgLTEuNDE1NjEsMi4zODkwMjEgMC4wMjc4LDEuNTY0ODk0IDAuMDA3LDMuMTMwNjQ1IDAuMDA3LDQuOTY1Mjk4IDAuMjY3NDIsLTAuMDEyMzYgMC44MzgxMiwwLjEyNDA3NiAxLjIwNjk5LC0wLjA4ODk2IDAuODE1NjcsLTAuNDcxMDg4IDEuNTI5ODMsLTEuMTE3OTUyIDIuNDI3NTgsLTEuODI0ODA1IHoiCiAgICAgICBpZD0icGF0aDY2LTQiCiAgICAgICBzdHlsZT0ic3Ryb2tlLXdpZHRoOjAuMjY0NTgzIgogICAgICAgaW5rc2NhcGU6bGFiZWw9IlIiIC8+PHBhdGgKICAgICAgIGZpbGw9IiNmOGY2ZmEiCiAgICAgICBvcGFjaXR5PSIxIgogICAgICAgc3Ryb2tlPSJub25lIgogICAgICAgZD0ibSA1MC42NzA4NjMsNDIuNDg5NjMzIGMgLTAuMjk0NDI2LC0xLjgyMzI5NiAtMC41MDk4NDEsLTMuNTQ0NDggLTAuOTExOTQ1LC01LjIyMDg4MiAtMC4yNjk0OTEsLTEuMTIzNTI0IDAuMTcyMDU2LC0xLjY4NzM1NyAxLjA1MDg2NSwtMi4yMTcxMjYgMi41MzY0MjUsLTEuNTI5MDEyIDUuMTI3NDgsLTEuMTE2MDA1IDcuNjMwODMsLTAuMDkwOTcgMS4xNDkxMiwwLjQ3MDUyNyAyLjIxNjgyLDEuMzIzNTM4IDMuMTAxMzMsMi4yMjAyMjQgMi4xNTI2NSwyLjE4MjI5NCAxLjQ3NDA0LDMuNzQxMjk5IC0wLjU3MTI3LDUuODA4NDMzIC0xLjgxNTQyLDEuODM0NzgyIC00LjI1MDY2LDIuNDIwMjMzIC02LjY3NjI0NiwyLjk1OTczNSAtMC40MTM5MTksMC4wOTIwNyAtMC44MjE3NDgsMC4yMTE1MSAtMS4wMjE4MDIsMC4yNjM1MzggMCwyLjYzMzQ2MyAtMC4wMiw1LjEzNjQ0MyAwLjAxMjcsNy42Mzg3MzMgMC4wMDkzLDAuNzExNjIgLTAuMDM0NzksMS40OTE3NSAtMC44NTI3MDcsMS40MzUxMiAtMC41MTYxNzMsLTAuMDM1NyAtMS4zMjQwMjMsLTAuNjQwNzIgLTEuNDE1NjgsLTEuMTA1NzUgLTAuMjQ4ODA2LC0xLjI2MjI4IC0wLjI2NDQ0NiwtMi41ODgzNiAtMC4yMjYxNDcsLTMuODg0ODIgMC4wNDY5NCwtMS41ODg4NiAwLjU5MzE4LC0zLjIwMjc1NSAtMC41MTYxMzYsLTQuNjc0MTM1IC0wLjEyNTYzLC0wLjE2NjY0MiAwLjAxNTI5LC0wLjU0NjMwMSAwLjA1NTgsLTAuODIzODA0IDAuMTA2NTExLC0wLjczMDAzNSAwLjIyNTc5OCwtMS40NTgyMDggMC4zNDA0MSwtMi4zMDgyOTQgbSAyLjU5NDIxNCwxLjIzNzI5MyBjIDIuNDY3NzE0LC0wLjIxMDUyOSA0LjU5NDk5NCwtMS4xMTYyODUgNi4yNDI1MzQsLTMuMDI2MDg3IDAuODkxMDksLTEuMDMyOTQ3IDEuMDcyMDYsLTIuMDQ4NDQ0IDAuMzU1OTEsLTIuNTc5NTE2IC0xLjczODExLC0xLjI4ODkyNiAtMy43MjEyMywtMS44NDM4MzkgLTUuODY4NjUyLC0xLjY3MTM0OSAtMC43NjQwNTMsMC4wNjEzNyAtMS4zNjE5NDYsMC4zNzY4NyAtMS4yNDk1MTEsMS40Mjg4NjQgMC4yMDIzMTEsMS44OTI5OTYgMC4yNTU4NTUsMy44MDE4OTMgMC41MTk3MTksNS44NDgwODggeiIKICAgICAgIGlkPSJwYXRoNjktMiIKICAgICAgIHN0eWxlPSJzdHJva2Utd2lkdGg6MC4yNjQ1ODMiCiAgICAgICBpbmtzY2FwZTpsYWJlbD0iUCIgLz48L2c+PC9nPjwvc3ZnPgo=")
const width = ref(200)
const height = ref(200)
const margin = ref(0)
const imageMargin = ref(-20)

const dotsOptionsColor = ref("#ffffff")
const dotsOptionsType = ref("classy-rounded")
const cornersSquareOptionsColor = ref("#ffffff")
const cornersSquareOptionsType = ref("dot")
const cornersDotOptionsColor = ref("#ffffff")
const cornersDotOptionsType = ref("dot")
const styleBorderRadius = ref(12)
const styledBorderRadiusFormatted = computed(() => `12px`)
const styleBackground = ref("#55298a")
const lastBackground = ref("#55298a")
const includeBackground = ref(true)
watch(
  includeBackground,
  (newIncludeBackground) => {
    if (!newIncludeBackground) {
      lastBackground.value = styleBackground.value
      styleBackground.value = "transparent"
    } else {
      styleBackground.value = lastBackground.value
    }
  },
  { immediate: true }
)

const dotsOptions = computed(() => ({
  color: dotsOptionsColor.value,
  type: dotsOptionsType.value
}))
const cornersSquareOptions = computed(() => ({
  color: cornersSquareOptionsColor.value,
  type: cornersSquareOptionsType.value
}))
const cornersDotOptions = computed(() => ({
  color: cornersDotOptionsColor.value,
  type: cornersDotOptionsType.value
}))
const style = computed(() => ({
  borderRadius: styledBorderRadiusFormatted.value,
  background: styleBackground.value
}))
const imageOptions = computed(() => ({
  margin: imageMargin.value
}))
const qrOptions = computed(() => ({
  errorCorrectionLevel: "Q"
}))

const qrCodeProps = computed<StyledQRCodeProps>(() => ({
  data: debouncedData.value || 'Have a beautiful day!',
  image: image.value,
  width: width.value,
  height: height.value,
  margin: margin.value,
  dotsOptions: dotsOptions.value,
  cornersSquareOptions: cornersSquareOptions.value,
  cornersDotOptions: cornersDotOptions.value,
  imageOptions: imageOptions.value,
  qrOptions: qrOptions.value
}))

// randomizeStyleSettings, uploadImage, and rest of your methods remain unchanged

//#region /* Frame settings */
const frameText = ref("Scan for more info")
const frameTextPosition = ref<'top' | 'bottom' | 'left' | 'right'>('bottom')
const showFrame = ref(true)
const frameStyle = ref<FrameStyle>({
  textColor: "#000000",
  backgroundColor: "#ffffff",
  borderColor: "#000000",
  borderWidth: "1px",
  borderRadius: "8px",
  padding: "16px"
})
const frameSettings = computed(() => ({
  text: frameText.value,
  position: frameTextPosition.value,
  style: frameStyle.value
}))
//#endregion

// ...rest of your script remains unchanged...
</script>

<template>
  <div
    class="flex items-start justify-center gap-4 md:flex-row md:gap-6 lg:gap-12 lg:pb-0"
    :style="mainDivPaddingStyle"
  >
    <!-- Sticky sidebar on large screens -->
    <div
      v-if="isLarge"
      ref="mainContentContainer"
      id="main-content-container"
      class="sticky top-0 flex w-full shrink-0 flex-col items-center justify-center p-4 md:w-fit"
    ></div>
    <!-- Bottom sheet on small screens -->
    <Drawer v-else>
      <DrawerTrigger
        id="drawer-preview-container"
        class="fixed inset-x-0 bottom-0 z-10 rounded-t-lg border-t border-solid border-slate-300 bg-white shadow-2xl outline-none focus-visible:ring-1 focus-visible:ring-zinc-700 dark:bg-black dark:focus-visible:ring-zinc-200"
      >
        <div class="flex flex-col items-center">
          <!-- Handle indicator for bottom sheet -->
          <div class="mt-2 h-1 w-16 rounded-full bg-gray-300 dark:bg-gray-700"></div>
          <div :class="['w-full', '-my-8']">
            <div class="flex origin-center scale-[0.7] items-center justify-center md:scale-100">
              <QRCodeFrame
                v-if="showFrame"
                :frame-text="frameText"
                :text-position="frameTextPosition"
                :frame-style="frameStyle"
              >
                <template #qr-code>
                  <div id="qr-code-container" class="grid place-items-center">
                    <div
                      class="grid place-items-center overflow-hidden"
                      :style="[
                        style,
                        {
                          width: `${PREVIEW_QRCODE_DIM_UNIT}px`,
                          height: `${PREVIEW_QRCODE_DIM_UNIT}px`
                        }
                      ]"
                    >
                      <StyledQRCode
                        v-bind="{
                          ...qrCodeProps,
                          data: data?.length > 0 ? data : t('Have nice day!'),
                          width: PREVIEW_QRCODE_DIM_UNIT,
                          height: PREVIEW_QRCODE_DIM_UNIT
                        }"
                        role="img"
                        aria-label="QR code"
                      />
                    </div>
                  </div>
                </template>
              </QRCodeFrame>
              <template v-else>
                <div class="grid place-items-center">
                  <div
                    class="grid place-items-center overflow-hidden"
                    :style="[
                      style,
                      {
                        width: `${PREVIEW_QRCODE_DIM_UNIT}px`,
                        height: `${PREVIEW_QRCODE_DIM_UNIT}px`
                      }
                    ]"
                  >
                    <StyledQRCode
                      v-bind="{
                        ...qrCodeProps,
                        data: data?.length > 0 ? data : t('Have nice day!'),
                        width: PREVIEW_QRCODE_DIM_UNIT,
                        height: PREVIEW_QRCODE_DIM_UNIT
                      }"
                      role="img"
                      aria-label="QR code preview"
                    />
                  </div>
                </div>
              </template>
            </div>
          </div>
          <div
            class="flex items-center gap-1 py-2 text-center text-sm text-gray-600 dark:text-gray-400"
          >
            <svg
              xmlns="http://www.w3.org/2000/svg"
              width="16"
              height="16"
              viewBox="0 0 24 24"
              class="inline"
            >
              <path fill="currentColor" d="M12 8l-6 6l1.41 1.41L12 10.83l4.59 4.58L18 14z" />
            </svg>
            {{ t('Export') }}
            <svg
              xmlns="http://www.w3.org/2000/svg"
              width="16"
              height="16"
              viewBox="0 0 24 24"
              class="inline"
            >
              <path fill="currentColor" d="M12 8l-6 6l1.41 1.41L12 10.83l4.59 4.58L18 14z" />
            </svg>
          </div>
        </div>
      </DrawerTrigger>
      <DrawerContent class="flex h-screen flex-col items-center justify-between">
        <div class="flex grow flex-col items-center justify-center gap-4">
          <DrawerTitle>{{ t('Export') }}</DrawerTitle>
          <div ref="mainContentContainer" id="main-content-container" class="w-full"></div>
        </div>
      </DrawerContent>
    </Drawer>

    <!-- Main content -->
    <Teleport to="#main-content-container" v-if="mainContentContainer != null">
      <div id="main-content">
        <div
          id="qr-code-container"
          :class="[
            'grid origin-center place-items-center',
            showFrame && ['left', 'right'].includes(frameTextPosition) && 'scale-[0.7] md:scale-100'
          ]"
        >
          <div v-if="showFrame" id="element-to-export">
            <QRCodeFrame
              :frame-text="frameText"
              :text-position="frameTextPosition"
              :frame-style="frameStyle"
            >
              <template #qr-code>
                <div id="qr-code-container" class="grid place-items-center">
                  <div
                    class="grid place-items-center overflow-hidden"
                    :style="[
                      style,
                      {
                        width: `${PREVIEW_QRCODE_DIM_UNIT}px`,
                        height: `${PREVIEW_QRCODE_DIM_UNIT}px`
                      }
                    ]"
                  >
                    <StyledQRCode
                      v-bind="{
                        ...qrCodeProps,
                        data: data?.length > 0 ? data : t('Have nice day!'),
                        width: PREVIEW_QRCODE_DIM_UNIT,
                        height: PREVIEW_QRCODE_DIM_UNIT
                      }"
                      role="img"
                      aria-label="QR code"
                    />
                  </div>
                </div>
              </template>
            </QRCodeFrame>
          </div>
          <div
            v-else
            id="element-to-export"
            class="grid place-items-center overflow-hidden"
            :style="[
              style,
              {
                width: `${PREVIEW_QRCODE_DIM_UNIT}px`,
                height: `${PREVIEW_QRCODE_DIM_UNIT}px`
              }
            ]"
          >
            <StyledQRCode
              v-bind="{
                ...qrCodeProps,
                data: data?.length > 0 ? data : t('Have nice day!'),
                width: PREVIEW_QRCODE_DIM_UNIT,
                height: PREVIEW_QRCODE_DIM_UNIT
              }"
              role="img"
              aria-label="QR code"
            />
          </div>
        </div>
        <div class="mt-4 flex flex-col items-center gap-8">
          <div class="flex flex-col items-center justify-center gap-3">
            <button
              v-if="exportMode !== ExportMode.Batch"
              id="copy-qr-image-button"
              class="button flex w-fit max-w-full flex-row items-center gap-1"
              @click="copyQRToClipboard"
              :disabled="isExportButtonDisabled"
              :title="
                isExportButtonDisabled
                  ? t('Please enter data to encode first')
                  : t('Copy QR Code to clipboard')
              "
              :aria-label="t('Copy QR Code to clipboard')"
            >
              <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24">
                <g
                  fill="none"
                  stroke="currentColor"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  stroke-width="2"
                >
                  <path d="M8 10a2 2 0 0 1 2-2h8a2 2 0 0 1 2 2v8a2 2 0 0 1-2 2h-8a2 2 0 0 1-2-2z" />
                  <path d="M16 8V6a2 2 0 0 0-2-2H6a2 2 0 0 0-2 2v8a2 2 0 0 0 2 2h2" />
                </g>
              </svg>
              <p>{{ t('Copy QR Code to clipboard') }}</p>
            </button>
            <button
              id="save-qr-code-config-button"
              class="button flex w-fit max-w-full flex-row items-center gap-1"
              @click="downloadQRConfig"
              :title="t('Save QR Code configuration')"
              :aria-label="t('Save QR Code configuration')"
            >
              <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24">
                <g
                  fill="none"
                  stroke="currentColor"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  stroke-width="2"
                >
                  <path d="M14 3v4a1 1 0 0 0 1 1h4" />
                  <path
                    d="M17 21H7a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h7l5 5v11a2 2 0 0 1-2 2zm-5-4v-6"
                  />
                  <path d="M9.5 13.5L12 11l2.5 2.5" />
                </g>
              </svg>
              <p>{{ t('Save QR Code configuration') }}</p>
            </button>
            <button
              id="load-qr-code-config-button"
              class="button flex w-fit max-w-full flex-row items-center gap-1"
              @click="loadQrConfigFromFile"
              :aria-label="t('Load QR Code configuration')"
            >
              <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24">
                <g
                  fill="none"
                  stroke="currentColor"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  stroke-width="2"
                >
                  <path d="M14 3v4a1 1 0 0 0 1 1h4" />
                  <path
                    d="M17 21H7a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h7l5 5v11a2 2 0 0 1-2 2zm-5-10v6"
                  />
                  <path d="M9.5 13.5L12 11l2.5 2.5" />
                </g>
              </svg>
              <p>{{ t('Load QR Code configuration') }}</p>
            </button>
          </div>
          <div id="export-options" class="grid place-items-center gap-4">
            <p class="text-zinc-900 dark:text-zinc-100">{{ t('Export as') }}</p>
            <div class="flex flex-row items-center justify-center gap-2">
              <button
                id="download-qr-image-button-png"
                class="button"
                @click="() => downloadQRImage('png')"
                :disabled="isExportButtonDisabled"
                :title="
                  isExportButtonDisabled
                    ? t('Please enter data to encode first')
                    : t('Download QR Code as PNG')
                "
                :aria-label="t('Download QR Code as PNG')"
              >
                <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24">
                  <g fill="none" stroke="currentColor" stroke-width="2">
                    <path d="M14 3v4a1 1 0 0 0 1 1h4" />
                    <path d="M5 12V5a2 2 0 0 1 2-2h7l5 5v4" />
                    <text
                      x="1"
                      y="22"
                      fill="currentColor"
                      stroke="none"
                      font-size="11px"
                      font-family="monospace"
                      font-weight="600"
                    >
                      PNG
                    </text>
                  </g>
                </svg>
              </button>
              <button
                id="download-qr-image-button-jpg"
                class="button"
                @click="() => downloadQRImage('jpg')"
                :disabled="isExportButtonDisabled"
                :title="
                  isExportButtonDisabled
                    ? t('Please enter data to encode first')
                    : t('Download QR Code as JPG')
                "
                :aria-label="t('Download QR Code as JPG')"
              >
                <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24">
                  <g fill="none" stroke="currentColor" stroke-width="2">
                    <path d="M14 3v4a1 1 0 0 0 1 1h4" />
                    <path d="M5 12V5a2 2 0 0 1 2-2h7l5 5v4" />
                    <text
                      x="1"
                      y="22"
                      fill="currentColor"
                      stroke="none"
                      font-size="11px"
                      font-family="monospace"
                      font-weight="600"
                    >
                      JPG
                    </text>
                  </g>
                </svg>
              </button>
              <button
                id="download-qr-image-button-svg"
                class="button"
                @click="() => downloadQRImage('svg')"
                :disabled="isExportButtonDisabled"
                :title="
                  isExportButtonDisabled
                    ? t('Please enter data to encode first')
                    : t('Download QR Code as SVG')
                "
                :aria-label="t('Download QR Code as SVG')"
              >
                <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24">
                  <g fill="none" stroke="currentColor" stroke-width="2">
                    <path d="M14 3v4a1 1 0 0 0 1 1h4" />
                    <path d="M5 12V5a2 2 0 0 1 2-2h7l5 5v4" />
                    <text
                      x="1"
                      y="22"
                      fill="currentColor"
                      stroke="none"
                      font-size="11px"
                      font-family="monospace"
                      font-weight="600"
                    >
                      SVG
                    </text>
                  </g>
                </svg>
              </button>
            </div>
          </div>
        </div>
      </div>
    </Teleport>

    <div id="settings" class="flex w-full grow flex-col items-start gap-8 text-start">
      <Accordion
        type="multiple"
        collapsible
        class="flex w-full flex-col gap-4"
        :default-value="['qr-code-settings']"
      >
        <AccordionItem value="frame-settings">
          <AccordionTrigger
            class="button !px-4 text-2xl text-gray-700 outline-none dark:text-gray-100 md:!px-6 lg:!px-8"
            ><span class="flex flex-row items-center gap-2"
              ><span id="frame-settings-title">{{ t('Frame settings') }}</span>
              <span
                class="rounded-full bg-white px-2 py-0.5 text-xs font-medium text-zinc-800 dark:bg-zinc-700 dark:text-zinc-200"
              >
                {{ t('New!') }}
              </span></span
            ></AccordionTrigger
          >
          <AccordionContent class="px-2 pb-8 pt-4">
            <section class="space-y-4" aria-labelledby="frame-settings-title">
              <div class="flex flex-row items-center gap-2">
                <label for="show-frame">{{ t('Add frame') }}</label>
                <input id="show-frame" type="checkbox" v-model="showFrame" />
              </div>

              <template v-if="showFrame">
                <div class="flex flex-col sm:flex-row sm:items-center sm:gap-8">
                  <div class="flex flex-col sm:w-1/2">
                    <label>{{ t('Frame preset') }}</label>
                    <Combobox
                      :items="allFramePresetOptions"
                      v-model:value="selectedFramePresetKey"
                      :button-label="t('Select frame preset')"
                    />
                  </div>
                </div>
                <div class="flex flex-col">
                  <label class="mb-2 block">{{ t('Text position') }}</label>
                  <fieldset class="flex-1" role="radio" tabindex="0">
                    <div
                      class="radio"
                      v-for="position in ['top', 'bottom', 'right', 'left']"
                      :key="position"
                    >
                      <input
                        :id="'frameTextPosition-' + position"
                        type="radio"
                        v-model="frameTextPosition"
                        :value="position"
                      />
                      <label :for="'frameTextPosition-' + position">{{ t(position) }}</label>
                    </div>
                  </fieldset>
                </div>

                <div>
                  <div class="mb-2 flex flex-row items-center gap-2">
                    <label for="frame-text">{{ t('Frame text') }}</label>
                  </div>
                  <textarea
                    name="frame-text"
                    class="text-input"
                    id="frame-text"
                    rows="2"
                    :placeholder="t('Scan for more info')"
                    v-model="frameText"
                  />
                </div>

                <div>
                  <label class="mb-2 block">{{ t('Frame style') }}</label>
                  <div class="grid grid-cols-1 gap-4 sm:grid-cols-2">
                    <div>
                      <label for="frame-text-color" class="mb-1 block text-sm">{{
                        t('Text color')
                      }}</label>
                      <input
                        id="frame-text-color"
                        type="color"
                        class="color-input"
                        v-model="frameStyle.textColor"
                      />
                    </div>
                    <div>
                      <label for="frame-bg-color" class="mb-1 block text-sm">{{
                        t('Background color')
                      }}</label>
                      <input
                        id="frame-bg-color"
                        type="color"
                        class="color-input"
                        v-model="frameStyle.backgroundColor"
                      />
                    </div>
                    <div>
                      <label for="frame-border-color" class="mb-1 block text-sm">{{
                        t('Border color')
                      }}</label>
                      <input
                        id="frame-border-color"
                        type="color"
                        class="color-input"
                        v-model="frameStyle.borderColor"
                      />
                    </div>
                    <div>
                      <label for="frame-border-width" class="mb-1 block text-sm">{{
                        t('Border width')
                      }}</label>
                      <input
                        id="frame-border-width"
                        type="text"
                        class="text-input"
                        v-model="frameStyle.borderWidth"
                        placeholder="1px"
                      />
                    </div>
                    <div>
                      <label for="frame-border-radius" class="mb-1 block text-sm">{{
                        t('Border radius')
                      }}</label>
                      <input
                        id="frame-border-radius"
                        type="text"
                        class="text-input"
                        v-model="frameStyle.borderRadius"
                        placeholder="8px"
                      />
                    </div>
                    <div>
                      <label for="frame-padding" class="mb-1 block text-sm">{{
                        t('Padding')
                      }}</label>
                      <input
                        id="frame-padding"
                        type="text"
                        class="text-input"
                        v-model="frameStyle.padding"
                        placeholder="16px"
                      />
                    </div>
                  </div>
                </div>
              </template>
            </section>
          </AccordionContent>
        </AccordionItem>
        <AccordionItem value="qr-code-settings">
          <AccordionTrigger
            class="button !px-4 text-2xl text-gray-700 outline-none dark:text-gray-100 md:!px-6 lg:!px-8"
            ><span id="qr-code-settings-title">{{ t('QR code settings') }}</span></AccordionTrigger
          >
          <AccordionContent class="px-2 pb-8 pt-4">
            <section class="space-y-8" aria-labelledby="qr-code-settings-title">
              <div>
                <label>{{ t('Preset') }}</label>
                <div class="flex flex-row items-center justify-start gap-2">
                  <Combobox
                    :items="allPresetOptions"
                    v-model:value="selectedPresetKey"
                    v-model:open="isPresetSelectOpen"
                    :button-label="t('Select QR code preset')"
                    :insert-divider-at-indexes="[0, 2]"
                  />
                  <button
                    class="button grid size-10 place-items-center"
                    @click="randomizeStyleSettings"
                    :aria-label="t('Randomize style')"
                  >
                    <svg
                      xmlns="http://www.w3.org/2000/svg"
                      width="24"
                      height="24"
                      viewBox="0 0 640 512"
                    >
                      <path
                        fill="#888888"
                        d="M274.9 34.3c-28.1-28.1-73.7-28.1-101.8 0L34.3 173.1c-28.1 28.1-28.1 73.7 0 101.8l138.8 138.8c28.1 28.1 73.7 28.1 101.8 0l138.8-138.8c28.1-28.1 28.1-73.7 0-101.8L274.9 34.3zM200 224a24 24 0 1 1 48 0a24 24 0 1 1-48 0zM96 200a24 24 0 1 1 0 48a24 24 0 1 1 0-48zm128 176a24 24 0 1 1 0-48a24 24 0 1 1 0 48zm128-176a24 24 0 1 1 0 48a24 24 0 1 1 0-48zm-128-80a24 24 0 1 1 0-48a24 24 0 1 1 0 48zm96 328c0 35.3 28.7 64 64 64h192c35.3 0 64-28.7 64-64V256c0-35.3-28.7-64-64-64H461.7c11.6 36 3.1 77-25.4 105.5L320 413.8V448zm160-120a24 24 0 1 1 0 48a24 24 0 1 1 0-48z"
                      />
                    </svg>
                  </button>
                </div>
              </div>
              <div class="w-full">
                <div class="flex w-full flex-col flex-wrap gap-4 sm:flex-row sm:gap-x-8">
                  <!-- Data to encode area -->
                  <div class="grow">
                    <!-- Header row: Label + Mode Toggles + Batch Options -->
                    <div class="mb-2 flex items-center gap-4">
                      <label for="data">{{ t('Data to encode') }}</label>
                      <!-- Mode Toggle Buttons -->
                      <div class="flex grow items-center gap-2">
                        <button
                          :class="[
                            'secondary-button',
                            { 'opacity-50': exportMode === ExportMode.Single } // Dim if active
                          ]"
                          @click="exportMode = ExportMode.Single"
                        >
                          {{ $t('Single export') }}
                        </button>
                        <button
                          :class="[
                            'secondary-button',
                            { 'opacity-50': exportMode === ExportMode.Batch } // Dim if active
                          ]"
                          @click="exportMode = ExportMode.Batch"
                        >
                          {{ $t('Batch export') }}
                        </button>
                        <!-- Batch specific options -->
                        <div
                          v-if="exportMode === ExportMode.Batch"
                          :class="[
                            'flex grow items-center justify-end gap-2',
                            dataStringsFromCsv.length > 0 && 'opacity-80'
                          ]"
                        ></div>
                      </div>
                    </div>
                    <!-- Single Mode Input -->
                    <div v-if="exportMode === ExportMode.Single" class="flex flex-col items-start">
                      <textarea
                        id="data"
                        v-model="data"
                        class="mr-2 grow text-input"
                        :placeholder="t('data to encode e.g. a URL or a string')"
                      ></textarea>
                      <button
                        @click="openDataModal"
                        aria-haspopup="dialog"
                        :aria-expanded="isDataModalVisible"
                        class="secondary-button mt-2 flex items-center gap-1 self-end"
                        :aria-label="t('Open data type generator')"
                      >
                        <span>{{ t('Data templates') }}</span>
                        <svg
                          xmlns="http://www.w3.org/2000/svg"
                          width="16"
                          height="16"
                          viewBox="0 0 24 24"
                        >
                          <!-- Icon from Tabler Icons by Paweł Kuna - https://github.com/tabler/tabler-icons/blob/master/LICENSE -->
                          <path
                            fill="none"
                            stroke="#888888"
                            stroke-linecap="round"
                            stroke-linejoin="round"
                            stroke-width="2"
                            d="m7 7l5 5l-5 5m6-10l5 5l-5 5"
                          />
                        </svg>
                      </button>
                    </div>
                    <template v-if="exportMode === ExportMode.Batch">
                      <template v-if="!inputFileForBatchEncoding">
                        <button
                          class="flex items-center justify-center rounded-lg border-2 border-dashed border-gray-300 p-1 py-4 text-center text-input"
                          :aria-label="t('Choose a CSV file containing data to encode')"
                          @click="fileInput?.click()"
                          @keyup.enter="fileInput?.click()"
                          @keyup.space="fileInput?.click()"
                          @dragover.prevent
                          @drop.prevent="onBatchInputFileUpload"
                        >
                          <div class="flex flex-col items-center">
                            <svg
                              xmlns="http://www.w3.org/2000/svg"
                              width="48"
                              height="48"
                              viewBox="0 0 24 24"
                              class="mb-2 text-gray-400"
                            >
                              <path
                                fill="currentColor"
                                d="M11 16V7.85l-2.6 2.6L7 9l5-5l5 5l-1.4 1.45l-2.6-2.6V16h-2Zm-5 4q-.825 0-1.413-.588T4 18v-3h2v3h12v-3h2v3q0 .825-.588 1.413T18 20H6Z"
                              />
                            </svg>
                            <p aria-hidden="true" class="text-sm">
                              {{ $t('Upload a CSV file') }}
                            </p>
                          </div>
                          <input
                            ref="fileInput"
                            type="file"
                            accept=".csv,.txt"
                            class="hidden"
                            @change="onBatchInputFileUpload"
                          />
                        </button>
                        <div class="flex flex-wrap justify-end gap-x-4 pt-1">
                          <p class="gap-1">
                            <a
                              href="/6_strings_batch.csv"
                              download
                              class="inline-flex items-center gap-1 text-sm text-zinc-500 outline-none hover:text-zinc-700 hover:underline focus-visible:ring-1 focus-visible:ring-zinc-700 dark:text-zinc-400 dark:hover:text-zinc-200 dark:focus-visible:ring-zinc-200"
                            >
                              {{ t('Example file (simple)') }}
                              <svg
                                xmlns="http://www.w3.org/2000/svg"
                                width="16"
                                height="16"
                                viewBox="0 0 24 24"
                                class="inline"
                              >
                                <path
                                  fill="currentColor"
                                  d="M12 15.575q-.2 0-.375-.063q-.175-.062-.325-.212l-3.6-3.6q-.275-.275-.275-.7q0-.425.275-.7q.275-.275.7-.275q.425 0 .7.275l1.9 1.9V4q0-.425.288-.713Q11.575 3 12 3t.713.287Q13 3.575 13 4v8.2l1.9-1.9q.275-.275.7-.275q.425 0 .7.275q.275.275.275.7q0 .425-.275.7l-3.6 3.6q-.15.15-.325.212q-.175.063-.375.063M6 21q-.825 0-1.413-.587Q4 19.825 4 19v-2q0-.425.287-.713Q4.575 16 5 16t.713.287Q6 16.575 6 17v2h12v-2q0-.425.288-.713Q18.575 16 19 16t.712.287Q20 16.575 20 17v2q0 .825-.587 1.413Q18.825 21 18 21m0-2q3.35 0 5.675-2.325T20 12t-2.325-5.675T12 4T6.325 6.325T4 12t2.325 5.675T12 20m.1-12.3q.625 0 1.088.4t.462 1q0 .55-.337.975t-.763.8q-.575.5-1.012 1.1t-.438 1.35q0 .35.263.588t.612.237q.375 0 .638-.25t.337-.625q.1-.525.45-.937t.75-.788q.575-.55.988-1.2t.412-1.45q0-1.275-1.037-2.087T12.1 6q-.95 0-1.812.4T8.975 7.625q-.175.3-.112.638t.337.512q.35.2.725.125t.625-.425q.275-.375.688-.575t.862-.2"
                                />
                              </svg>
                            </a>
                          </p>
                          <p class="gap-1">
                            <a
                              href="/vcard_sample.csv"
                              download
                              class="inline-flex items-center gap-1 text-sm text-zinc-500 outline-none hover:text-zinc-700 hover:underline focus-visible:ring-1 focus-visible:ring-zinc-700 dark:text-zinc-400 dark:hover:text-zinc-200 dark:focus-visible:ring-zinc-200"
                            >
                              {{ t('Example file (vCard)') }}
                              <svg
                                xmlns="http://www.w3.org/2000/svg"
                                width="16"
                                height="16"
                                viewBox="0 0 24 24"
                                class="inline"
                              >
                                <path
                                  fill="currentColor"
                                  d="M12 15.575q-.2 0-.375-.063q-.175-.062-.325-.212l-3.6-3.6q-.275-.275-.275-.7q0-.425.275-.7q.275-.275.7-.275q.425 0 .7.275l1.9 1.9V4q0-.425.288-.713Q11.575 3 12 3t.713.287Q13 3.575 13 4v8.2l1.9-1.9q.275-.275.7-.275q.425 0 .7.275q.275.275.275.7q0 .425-.275.7l-3.6 3.6q-.15.15-.325.212q-.175.063-.375.063M6 21q-.825 0-1.413-.587Q4 19.825 4 19v-2q0-.425.287-.713Q4.575 16 5 16t.713.287Q6 16.575 6 17v2h12v-2q0-.425.288-.713Q18.575 16 19 16t.712.287Q20 16.575 20 17v2q0 .825-.587 1.413Q18.825 21 18 21m0-2q3.35 0 5.675-2.325T20 12t-2.325-5.675T12 4T6.325 6.325T4 12t2.325 5.675T12 20m.1-12.3q.625 0 1.088.4t.462 1q0 .55-.337.975t-.763.8q-.575.5-1.012 1.1t-.438 1.35q0 .35.263.588t.612.237q.375 0 .638-.25t.337-.625q.1-.525.45-.937t.75-.788q.575-.55.988-1.2t.412-1.45q0-1.275-1.037-2.087T12.1 6q-.95 0-1.812.4T8.975 7.625q-.175.3-.112.638t.337.512q.35.2.725.125t.625-.425q.275-.375.688-.575t.862-.2"
                                />
                              </svg>
                            </a>
                          </p>
                        </div>
                      </template>
                      <div v-else-if="isValidCsv" class="p-4 text-center">
                        <div v-if="isBatchExportSuccess">
                          <p>{{ $t('QR codes have been successfully exported.') }}</p>
                          <button class="button mt-4" @click="inputFileForBatchEncoding = null">
                            {{ $t('Start new batch export') }}
                          </button>
                        </div>
                        <div v-else-if="currentExportedQrCodeIndex == null && !isExportingBatchQRs">
                          <div v-if="dataStringsFromCsv.length > 0" class="mt-4">
                            <div
                              class="flex flex-col gap-2 rounded-lg border border-gray-200 bg-gray-50 p-4 dark:border-gray-700 dark:bg-gray-800"
                            >
                              <div v-if="previewRow && 'firstName' in previewRow">
                                <VCardPreview :vCard="previewRow" />
                              </div>
                              <div v-else>
                                <div class="space-y-2">
                                  <div class="flex flex-col gap-1">
                                    <span
                                      class="text-xs font-medium text-gray-500 dark:text-gray-400"
                                      >{{ $t('Data:') }}</span
                                    >
                                    <code
                                      class="rounded bg-white px-2 py-1 font-mono text-sm dark:bg-gray-900"
                                    >
                                      {{ dataStringsFromCsv[previewRowIndex] }}
                                    </code>
                                  </div>
                                  <div v-if="frameTextsFromCsv.length > 0">
                                    <span
                                      class="text-xs font-medium text-gray-500 dark:text-gray-400"
                                      >{{ $t('Frame text:') }}</span
                                    >
                                    <code
                                      class="rounded bg-white px-2 py-1 font-mono text-sm dark:bg-gray-900"
                                    >
                                      {{ frameTextsFromCsv[previewRowIndex + 1] }}
                                    </code>
                                  </div>
                                </div>
                              </div>
                              <div class="mt-2 flex items-center justify-between">
                                <button
                                  class="rounded bg-gray-200 px-2 py-1 text-gray-700 disabled:opacity-50 dark:bg-gray-700 dark:text-gray-200 dark:disabled:opacity-60"
                                  :disabled="previewRowIndex === 0"
                                  @click="previewRowIndex--"
                                >
                                  &lt;
                                </button>
                                <span class="text-xs text-gray-500 dark:text-gray-400"
                                  >{{ previewRowIndex + 1 }} / {{ dataStringsFromCsv.length }}</span
                                >
                                <button
                                  class="rounded bg-gray-200 px-2 py-1 text-gray-700 disabled:opacity-50 dark:bg-gray-700 dark:text-gray-200 dark:disabled:opacity-60"
                                  :disabled="previewRowIndex === dataStringsFromCsv.length - 1"
                                  @click="previewRowIndex++"
                                >
                                  &gt;
                                </button>
                              </div>
                            </div>
                          </div>
                        </div>
                        <div v-else-if="currentExportedQrCodeIndex != null">
                          <p>{{ $t('Creating QR codes... This may take a while.') }}</p>
                          <p>
                            {{
                              $t('{index} / {count} QR codes have been created.', {
                                index: currentExportedQrCodeIndex + 1,
                                count: dataStringsFromCsv.length
                              })
                            }}
                          </p>
                        </div>
                      </div>
                      <div v-else class="p-4 text-center text-red-500">
                        <p>{{ $t('Invalid CSV') }}</p>
                      </div>
                    </template>
                  </div>

                  <!-- Error correction level -->
                  <div class="shrink-0">
                    <fieldset class="h-full" role="radio" tabindex="0">
                      <div class="flex flex-row items-center gap-2">
                        <legend>{{ t('Error correction level') }}</legend>
                        <a
                          href="https://docs.uniqode.com/en/articles/7219782-what-is-the-recommended-error-correction-level-for-printing-a-qr-code"
                          target="_blank"
                          class="icon-button flex flex-row items-center"
                          :aria-label="t('What is error correction level?')"
                        >
                          <svg
                            class="me-1"
                            xmlns="http://www.w3.org/2000/svg"
                            width="16"
                            height="16"
                            viewBox="0 0 24 24"
                          >
                            <path
                              fill="#888888"
                              d="M11.95 18q.525 0 .888-.363t.362-.887t-.362-.888t-.888-.362t-.887.363t-.363.887t.363.888t.887.362m.05 4q-2.075 0-3.9-.788t-3.175-2.137T2.788 15.9T2 12t.788-3.9t2.137-3.175T8.1 2.788T12 2t3.9.788t3.175 2.137T21.213 8.1T22 12t-.788 3.9t-2.137 3.175t-3.175 2.138T12 22m0-2q3.35 0 5.675-2.325T20 12t-2.325-5.675T12 4T6.325 6.325T4 12t2.325 5.675T12 20m.1-12.3q.625 0 1.088.4t.462 1q0 .55-.337.975t-.763.8q-.575.5-1.012 1.1t-.438 1.35q0 .35.263.588t.612.237q.375 0 .638-.25t.337-.625q.1-.525.45-.937t.75-.788q.575-.55.988-1.2t.412-1.45q0-1.275-1.037-2.087T12.1 6q-.95 0-1.812.4T8.975 7.625q-.175.3-.112.638t.337.512q.35.2.725.125t.625-.425q.275-.375.688-.575t.862-.2"
                            />
                          </svg>
                        </a>
                      </div>
                      <div v-for="level in errorCorrectionLevels" class="radio" :key="level">
                        <input
                          :id="'errorCorrectionLevel-' + level"
                          type="radio"
                          v-model="errorCorrectionLevel"
                          :value="level"
                          :aria-describedby="
                            level === recommendedErrorCorrectionLevel
                              ? 'recommended-text'
                              : undefined
                          "
                        />
                        <div class="flex items-center gap-2">
                          <label :for="'errorCorrectionLevel-' + level">{{
                            t(ERROR_CORRECTION_LEVEL_LABELS[level])
                          }}</label>
                          <span
                            v-if="level === recommendedErrorCorrectionLevel"
                            class="inline-flex items-center rounded-full bg-zinc-100 px-2 py-0.5 text-xs font-medium text-zinc-800 dark:bg-zinc-700 dark:text-zinc-200"
                          >
                            {{ t('Suggested') }}
                          </span>
                        </div>
                      </div>
                    </fieldset>
                  </div>
                </div>
              </div>
              <div class="w-full">
                <div class="mb-2 flex flex-row items-center gap-2">
                  <label for="image-url">
                    {{ t('Logo image URL') }}
                  </label>
                  <button class="icon-button flex flex-row items-center" @click="uploadImage">
                    <svg
                      xmlns="http://www.w3.org/2000/svg"
                      width="24"
                      height="24"
                      viewBox="0 0 24 24"
                    >
                      <g
                        fill="none"
                        stroke="currentColor"
                        stroke-linecap="round"
                        stroke-linejoin="round"
                        stroke-width="2"
                      >
                        <path d="M14 3v4a1 1 0 0 0 1 1h4" />
                        <path
                          d="M17 21H7a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h7l5 5v11a2 2 0 0 1-2 2zm-5-10v6"
                        />
                        <path d="M9.5 13.5L12 11l2.5 2.5" />
                      </g>
                    </svg>
                    <span>{{ t('Upload image') }}</span>
                  </button>
                </div>
                <textarea
                  name="image-url"
                  class="text-input"
                  id="image-url"
                  rows="1"
                  :placeholder="t('Logo image URL')"
                  v-model="image"
                />
              </div>
              <div class="flex flex-row items-center gap-2">
                <label for="with-background">
                  {{ t('With background') }}
                </label>
                <input id="with-background" type="checkbox" v-model="includeBackground" />
              </div>
              <div id="color-settings" :class="'flex w-full flex-row flex-wrap gap-4'">
                <div
                  :inert="!includeBackground"
                  :class="[!includeBackground && 'opacity-30', 'flex flex-row items-center gap-2']"
                >
                  <label for="background-color">{{ t('Background color') }}</label>
                  <input
                    id="background-color"
                    type="color"
                    class="color-input"
                    v-model="styleBackground"
                  />
                </div>
                <div class="flex flex-row items-center gap-2">
                  <label for="dots-color">{{ t('Dots color') }}</label>
                  <input
                    id="dots-color"
                    type="color"
                    class="color-input"
                    v-model="dotsOptionsColor"
                  />
                </div>
                <div class="flex flex-row items-center gap-2">
                  <label for="corners-square-color">{{ t('Corners Square color') }}</label>
                  <input
                    id="corners-square-color"
                    type="color"
                    class="color-input"
                    v-model="cornersSquareOptionsColor"
                  />
                </div>
                <div class="flex flex-row items-center gap-2">
                  <label for="corners-dot-color">{{ t('Corners Dot color') }}</label>
                  <input
                    id="corners-dot-color"
                    type="color"
                    class="color-input"
                    v-model="cornersDotOptionsColor"
                  />
                </div>
              </div>
              <div class="flex w-full flex-col gap-4 sm:flex-row sm:gap-8">
                <div class="w-full sm:w-1/3">
                  <label for="width">
                    {{ t('Width (px)') }}
                  </label>
                  <input
                    class="text-input"
                    id="width"
                    type="number"
                    placeholder="width in pixels"
                    v-model="width"
                  />
                </div>
                <div class="w-full sm:w-1/3">
                  <label for="height">
                    {{ t('Height (px)') }}
                  </label>
                  <input
                    class="text-input"
                    id="height"
                    type="number"
                    placeholder="height in pixels"
                    v-model="height"
                  />
                </div>
                <div class="w-full sm:w-1/3">
                  <label for="border-radius">
                    {{ t('Border radius (px)') }}
                  </label>
                  <input
                    class="text-input"
                    id="border-radius"
                    type="number"
                    placeholder="24"
                    v-model="styleBorderRadius"
                  />
                </div>
              </div>
              <div class="flex w-full flex-col gap-4 sm:flex-row sm:gap-8">
                <div class="w-full sm:w-1/2">
                  <label for="margin">
                    {{ t('Margin (px)') }}
                  </label>
                  <input
                    class="text-input"
                    id="margin"
                    type="number"
                    placeholder="0"
                    v-model="margin"
                  />
                </div>
                <div class="w-full sm:w-1/2">
                  <label for="image-margin">
                    {{ t('Image margin (px)') }}
                  </label>
                  <input
                    class="text-input"
                    id="image-margin"
                    type="number"
                    placeholder="0"
                    v-model="imageMargin"
                  />
                </div>
              </div>
              <div
                id="dots-squares-settings"
                class="mb-4 flex w-full flex-col flex-wrap gap-6 md:flex-row"
              >
                <fieldset class="flex-1" role="radio" tabindex="0">
                  <legend>{{ t('Dots type') }}</legend>
                  <div
                    class="radio"
                    v-for="type in [
                      'dots',
                      'rounded',
                      'classy',
                      'classy-rounded',
                      'square',
                      'extra-rounded'
                    ]"
                    :key="type"
                  >
                    <input
                      :id="'dotsOptionsType-' + type"
                      type="radio"
                      v-model="dotsOptionsType"
                      :value="type"
                    />
                    <label :for="'dotsOptionsType-' + type">{{ t(type) }}</label>
                  </div>
                </fieldset>
                <fieldset class="flex-1" role="radio" tabindex="0">
                  <legend>{{ t('Corners Square type') }}</legend>
                  <div class="radio" v-for="type in ['dot', 'square', 'extra-rounded']" :key="type">
                    <input
                      :id="'cornersSquareOptionsType-' + type"
                      type="radio"
                      v-model="cornersSquareOptionsType"
                      :value="type"
                    />
                    <label :for="'cornersSquareOptionsType-' + type">{{ t(type) }}</label>
                  </div>
                </fieldset>
                <fieldset class="flex-1" role="radio" tabindex="0">
                  <legend>{{ t('Corners Dot type') }}</legend>
                  <div class="radio" v-for="type in ['dot', 'square']" :key="type">
                    <input
                      :id="'cornersDotOptionsType-' + type"
                      type="radio"
                      v-model="cornersDotOptionsType"
                      :value="type"
                    />
                    <label :for="'cornersDotOptionsType-' + type">{{ t(type) }}</label>
                  </div>
                </fieldset>
              </div>
            </section>
          </AccordionContent>
        </AccordionItem>
      </Accordion>
    </div>
  </div>

  <DataTemplatesModal
    :show="isDataModalVisible"
    :initial-data="data"
    @close="closeDataModal"
    @update:data="updateDataFromModal"
  />

  <!-- Fallback modal for manual copy in Safari -->
  <CopyImageModal
    v-if="showSafariCopyImageModal"
    :is-loading="copyModalIsLoading"
    :image-src="copyModalImageSrc"
    @close="closeCopyModal"
  />
</template>
