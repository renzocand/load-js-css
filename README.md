# Aqui esta el ARCHIVO
```
// Source Code:  https://medium.com/better-programming/angular-load-external-javascript-file-dynamically-3d14dde815cb

interface Scripts {
    name: string;
    src: string;
    element: 'style' | 'script';
}

declare var document: any;


export class DynamicScriptLoader {

    private scripts: any = {};

    constructor() {
        // ScriptStore.forEach((script: any) => {
        //     this.scripts[script.name] = {
        //         loaded: false,
        //         src: script.src
        //     };
        // });
    }

    loadScripts(args: Scripts[], callback?: any) {
        this.load(args).then(data => {
            if (callback) callback();
        }).catch(error => console.log(error));
    }

    load(scripts: any) {
        const promises: any[] = [];
        scripts.forEach((script) => promises.push(this.loadScriptByObject(script)));
        return Promise.all(promises);
    }
    private loadScriptByObject(script: Scripts) {
        return new Promise((resolve, reject) => {
            if (!this.scripts[script.name]) {
                this.scripts[script.name] = { name: script.name, src: script.src, loaded: false, element: script.element };
            }
            if (!this.isScriptLoaded(this.scripts[script.name].src) && !this.scripts[script.name].loaded) {
                if (this.scripts[script.name].element == 'script') {//load script
                    let _script = document.createElement('script');
                    _script.type = 'text/javascript';
                    _script.src = this.scripts[script.name].src;
                    if (_script.readyState) {  //IE
                        _script.onreadystatechange = () => {
                            if (_script.readyState === "loaded" || _script.readyState === "complete") {
                                _script.onreadystatechange = null;
                                this.scripts[script.name].loaded = true;
                                resolve({ script: script.name, loaded: true, status: 'Loaded' });
                            }
                        };
                    } else {  //Others
                        _script.onload = () => {
                            this.scripts[script.name].loaded = true;
                            resolve({ script: script.name, loaded: true, status: 'Loaded' });
                        };
                    }
                    _script.onerror = (error: any) => resolve({ script: script.name, loaded: false, status: 'Loaded' });
                    document.getElementsByTagName('head')[0].appendChild(_script);
                }
                else if (this.scripts[script.name].element == 'style') {
                    let _style = document.createElement('link');
                    _style.rel = 'stylesheet';
                    _style.type = 'text/css';
                    _style.href = this.scripts[script.name].src;
                    if (_style.readyState) {  //IE
                        _style.onreadystatechange = () => {
                            if (_style.readyState === "loaded" || _style.readyState === "complete") {
                                _style.onreadystatechange = null;
                                this.scripts[script.name].loaded = true;
                                resolve({ script: script.name, loaded: true, status: 'Loaded' });
                            }
                        };
                    } else {  //Others
                        _style.onload = () => {
                            this.scripts[script.name].loaded = true;
                            resolve({ script: script.name, loaded: true, status: 'Loaded' });
                        };
                    }
                    _style.onerror = (error: any) => resolve({ script: script.name, loaded: false, status: 'Loaded' });
                    document.getElementsByTagName('head')[0].appendChild(_style);
                }
            } else {
                resolve({ script: script.name, loaded: true, status: 'Already Loaded' });
            }
        });
    }
    private loadScript(name: string) {
        return new Promise((resolve, reject) => {
            if (!this.scripts[name].loaded) {
                //load script
                let script = document.createElement('script');
                script.type = 'text/javascript';
                script.src = this.scripts[name].src;
                if (script.readyState) {  //IE
                    script.onreadystatechange = () => {
                        if (script.readyState === "loaded" || script.readyState === "complete") {
                            script.onreadystatechange = null;
                            this.scripts[name].loaded = true;
                            resolve({ script: name, loaded: true, status: 'Loaded' });
                        }
                    };
                } else {  //Others
                    script.onload = () => {
                        this.scripts[name].loaded = true;
                        resolve({ script: name, loaded: true, status: 'Loaded' });
                    };
                }
                script.onerror = (error: any) => resolve({ script: name, loaded: false, status: 'Loaded' });
                document.getElementsByTagName('head')[0].appendChild(script);
            } else {
                resolve({ script: name, loaded: true, status: 'Already Loaded' });
            }
        });
    }

    private isScriptLoaded(src: string) {
        if (src !== '') {
            let _scripts = Object.assign([], document.getElementsByTagName('script'));
            if (_scripts.length > 0) {
                return _scripts.filter(script => script.src.includes(src)).length > 0;
            } else return false
        } else return false;
    }

}
```
