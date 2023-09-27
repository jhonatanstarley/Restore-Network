@echo off
chcp 65001
cls

rem Função para exibir informações de ajuda e menu principal
:inicio
cls
echo.
echo _________________________________________________________
echo _________________________________________________________
echo - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
echo *************   Script Restaura Internet   **************
echo - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
echo _________________________________________________________
echo _________________________________________________________
echo                           Script by Jhonatan S.C
echo.
echo.
echo Escolha uma das opções abaixo:
echo.
echo   [1] Restaurar configurações de rede
echo   [2] Sair sem executar o script
echo.
echo.
set /p opcao=Digite o número da opção desejada:

if "%opcao%" == "1" goto :restaurarRede
if "%opcao%" == "2" goto :sair

rem Se a opção não for válida, exibe uma mensagem de erro
goto :erro

rem Função para exibir uma mensagem de erro e retornar ao menu inicial
:erro
cls
echo.
echo Escolha uma opção válida ou feche o script.
echo.
pause > nul
goto :inicio

rem Função para verificar se o script está sendo executado com privilégios elevados
:verificarPrivilegios
net session >nul 2>&1 "%SYSTEMROOT%\System32\cacls.exe" "%SYSTEMROOT%\System32\config\system"

if "%errorLevel%" neq "0" (
    echo.
    echo Este script requer privilégios de administrador para ser executado corretamente.
    echo Por favor, execute-o como administrador.
    echo.
    pause
    exit /b
)

rem Função para restaurar as configurações de rede
:restaurarRede
cls
echo.
echo _________________________________________________________
echo _________________________________________________________
echo - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
echo **********   Restaurando configurações de rede   *********
echo - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
echo _________________________________________________________
echo _________________________________________________________
echo.
echo Restaurando as configurações de rede...

call :verificarPrivilegios


arp -d * 
nbtstat -R
nbtstat -RR
ipconfig /flushdns
ipconfig /registerdns
ipconfig /release
ipconfig /renew

netsh winsock reset
netsh winhttp reset proxy
netsh winsock reset catalog
netsh winsock reset all
netsh interface ip delete arpcache
netsh advfirewall reset
netsh branchcache reset
netsh int ip reset all
netsh int ip reset c:\Temp\resetlog.txt
netsh int ipv4 reset all
netsh int ipv6 reset all
netsh int ip reset all
netsh int httpstunnel reset all
netsh int isatap reset all
netsh int portproxy reset all
netsh int tcp reset all
netsh int teredo reset all
net localgroup administradores localservice /add
fsutil resource setautoreset true C:\
netsh winhttp import proxy source=ie
sc config DcomLaunch start =auto

sc config wuauserv start= auto
sc config bits start= auto 
sc config cryptsvc start= auto 
sc config trustedinstaller start= auto 


cls
echo.
echo.
echo ********   Operação executada com sucesso!   ********
echo.
pause

rem Pergunta ao usuário se deseja reiniciar o computador
:reiniciar
cls
echo.
echo Para aplicar as atualizações, é necessário reiniciar o computador.
echo.
echo Deseja reiniciar agora?
echo.
echo   [S] Sim
echo   [N] Não
echo.
set /p opcaof=Digite a opção desejada:

if /i "%opcaof%" == "S" goto :sreiniciar
if /i "%opcaof%" == "N" goto :nreiniciar

rem Se a opção não for válida, exibe uma mensagem de erro
goto :erro

rem Função para reiniciar o computador
:sreiniciar
shutdown -r -t 120
exit

rem Função para encerrar o script sem reiniciar o computador
:nreiniciar
exit

rem Encerrar o script sem executar nenhuma ação
:sair
cls
echo.
echo Quando quiser arrumar sua internet, volte e execute este script!
echo.
echo                       Até mais!
echo.
pause
