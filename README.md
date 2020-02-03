# HTTPie-Windows
Stand-alone Windows binary for [HTTPie](https://github.com/jakubroztocil/httpie) version `2.1.0-dev`.

This executable is provided on the [release page](https://github.com/jftuga/HTTPie-Windows/releases).

I was able to successfully build a stand-alone `http.exe` binary inside of the `mcr.microsoft.com/windows/servercore:ltsc2019` Docker container 
with `Python 3.7.6` and `PyInstaller 3.6`. 

`Docker Desktop 2.1.0.5` was also used.

[HTTPie Windows packaging discussion](https://github.com/jakubroztocil/httpie/issues/58)

How to build...

Outside the Docker container:

    mkdir c:\temp\outside
    docker run -t -i --mount type=bind,src=c:\temp\outside,dst=c:\inside -w c:\ mcr.microsoft.com/windows/servercore:ltsc2019

You are now inside the Docker container:

    curl -L https://aka.ms/nugetclidl -o nuget.exe
    set PATH=%PATH%;c:\python\tools;c:\python\tools\scripts;c:\GitForWindows\tools\cmd
    nuget install GitForWindows -ExcludeVersion
    set PYVER=3.7.6
    nuget install python -ExcludeVersion -Version %PYVER% -OutputDirectory .
    python -m pip install --no-warn-script-location PyInstaller
    python -m pip install pywin32
    git clone https://github.com/jakubroztocil/httpie.git
    cd httpie
    python -m pip install -r requirements-dev.txt
    python setup.py build
    cd build\lib\httpie
    echo sys.exit(main())>> core.py
    pyinstaller -F --noupx -n http.exe core.py
    cd dist
    copy http.exe c:\inside
    exit

You are now outside of the Docker container:

    dir c:\temp\outside\http.exe

The resulting `http.exe` has a file size of `11,600,755`. 

## Warnings

I have only done minimal testing with this binary and can not confirm that all features and command-line options work as expected. Therefore, use at your own risk!

* **VirusTotal Check**
* https://www.virustotal.com/gui/file/21e5769c334ae619baf9361b4ef364cfdb0743f887b2a1c01f55f875c9520d1f/detection
* Note that some the of AV programs including *Jiangmin*, *Zillya*, and *TrapMine* have classified this binary as unsafe.
* Even though these are **false positives**, use this binary at your own risk!
