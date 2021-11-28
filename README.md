# Pruebas de Flux V2

## Actualizar Flux

* Nosotros instalamos v0.6.3 (aprox.), pero actualmente va por v0.24.0

    * No he visto que haya compatibilidad, intentar actualizar provoca un fallo importante.
    * La única opción es desinstalar (nos objetos sincronizados NO se borran)
    * Volver a instalar una nueva versión (limpia)
    * Adaptar los ficheros a la nueva versión

* El namespace flux-system desaparece de modo que lo primero es:

    * Revisar todo lo que va a desaparecer cuando el namespace flux-system se vaya de fiesta: secretos
    * Hacer una copia de seguridad de la master key de Sealed Secrets (interesa la activa).

        `kubectl get secret -n flux-system -l sealedsecrets.bitnami.com/sealed-secrets-key -o yaml > master.key`

        `openssl x509 -enddate -noout -in /path/to/tls.crt`

    * Hacer copias o recuperar los GITLAB_USER/TOKEN usados para desplegar originalmente.

        `kubectl get secret -n flux-system flux-system -o yaml > gitlab.key`

* Desconecta todos los elementos de flux que despliegan para que no afecte desinstalar

    `flux get ks`
    `flux suspend ks <name>`

    `flux get helmreleases`
    `flux suspend helmrelease <name>`

* Desinstala flux

    `flux uninstall --crds`

* Descarga el nuevo cliente y renombra el viejo como flux063
* Instala flux vas a necesitar el gitlab user/token que usabas para evitar fiestas

    `flux bootstrap gitlab --components-extra=image-reflector-controller,image-automation-controller --owner=juanjmerono --repository=fluxv2-demo --branch=main --path=./clusters/mix-cluster-0240 --private=false --token-auth --personal --version=v0.24.0`

* Despliega la master.key en el nuevo flux-system
* Migra los elementos uno a uno al nuevo esquema
    * No veo posible mantener funcionando dos versiones de flux diferentes en el mismo cluster por los CRDs
    * Lo he intentado pero no es posible los CRDs CHOCAN.
* Copia elementos del flux-system y de la vieja carpeta a la nueva

    * Se ven afectados objetos flux:
        Kustomization: v1beta2
        ImageUpdateAutomation: v1beta1
    * Son cambios mínimos en el primero y algo más delicados en el segundo caso
