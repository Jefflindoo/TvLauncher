# Meu Launcher para Google TV / Chromecast (Kotlin nativo, v3)

## Novidades da v3
- Busca: botão "Buscar" (texto) e "Voz" (RecognizerIntent, pt-BR). Voz com resultado único abre o app direto
  ("abrir Netflix"). Busca ignora acentos e maiúsculas. Voltar limpa a busca.
- Aba "Recentes" (últimos 12 apps abertos, mais novo primeiro).
- Temas de cor (Ciano, Roxo, Verde, Âmbar, Gótico vermelho) em Ajustes; trocam acento e fundo via atributos de tema.
- Favoritos reordenáveis: segure OK no favorito > "Mover para esquerda/direita".
- Visual dos apps: Ícones ou Banners 16:9 (Ajustes). Favoritos ficam sempre compactos; apps sem banner
  mostram o ícone centralizado no card.
- Desempenho/robustez: ícones e banners viram Bitmaps em cache (128px / 256x144), recarga só dos apps novos,
  eventos de instalação agrupados (debounce), app com recurso quebrado não derruba a lista, falhas ao abrir
  app não fecham o launcher, wallpaper liberado em pouca memória, clima tenta de novo em 2 min se falhar,
  R8 + shrinkResources no release.

## Por que nativo (e não WebView)
Chromecast tem pouca RAM/CPU. WebView carrega um navegador inteiro e o foco por D-Pad em HTML é frágil.

## Estrutura
- `data/`   AppRepository (PackageManager + cache), Categorizer, PrefsStore, WeatherRepository, WallpaperLoader
- `system/` NetworkMonitor, HomeKeyService (Acessibilidade), BootReceiver
- `ui/`     MainActivity, AppAdapter (zoom + borda no foco, modo ícone/banner), Dialogs, Themes

## Controle remoto
OK = abrir | segurar OK (ou MENU) = menu do app | Voltar = limpa busca / sai da aba / volta ao topo

## Compilar
Android Studio > abrir a pasta > (Gradle 8.7+ se pedir) > Build > Build APK(s)
-> app/build/outputs/apk/debug/app-debug.apk

## Instalar
Na TV: Configurações > Sistema > Sobre > 7 toques em "Build"; ative Depuração USB/ADB.
    adb connect IP_DA_TV:5555
    adb install -r app-debug.apk

## Home + iniciar ao ligar
1) Desativar o launcher do Google (reversível; troque disable-user por enable para desfazer):
    adb shell pm disable-user --user 0 com.google.android.apps.tv.launcherx
    adb shell pm disable-user --user 0 com.google.android.tvrecommendations
2) Complemento (mantendo o launcher do Google ativo):
    adb shell appops set com.jefferson.tvlauncher SYSTEM_ALERT_WINDOW allow
    adb shell settings put secure enabled_accessibility_services com.jefferson.tvlauncher/com.jefferson.tvlauncher.system.HomeKeyService
    adb shell settings put secure accessibility_enabled 1

## Papel de parede HD
adb push foto.jpg /sdcard/Android/data/com.jefferson.tvlauncher/files/wallpaper.jpg  (ou Ajustes > URL)

## Clima
Open-Meteo, sem chave. Sem cidade definida usa localização aproximada pelo IP. Troque em Ajustes.

## Voz
Depende de um serviço de voz instalado (o app do Google no Google TV). Se não houver, o launcher avisa.
