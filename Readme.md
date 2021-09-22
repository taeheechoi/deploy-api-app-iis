1. Install IIS
    - Turn Windows features on or off
    
    - Check "Internet Information Services"
    
    - Internet Information Services >> World Wide Web Services >> Application Deployment Features >> Check "CGI"

2. Deploy Django API to IIS
    - pip install wfastcgi on virtual enviroment
    
    - add STATIC_ROOT =  BASE_DIR / 'static' in settings.py
    
    - python manage.py collectstatic

    - Right click on "Sites" >> Add Website
    
    - Enter "Site name", "Physical path", "IP Address", "Port"
        eg) 
        Site: LottoComboAPI, 
        Physical path: C:\Developments\python-lotto-combo-api\lottocomboapi
        IP Address: 192.168.1.100
        Port: 8200

    - Open Handler Mappings inside of website (eg. LottoComboAPI)
    
    - Click "Add Module Mapping"
    
    - Enter "Request path", "Module", "Executable", "Name"
        eg) 
        Request path: *
        Module: FastCgiModule
        Executable: C:\Developments\python-lotto-combo-api\lottocomboapi\venv\Scripts\python.exe|C:\Developments\python-lotto-combo-api\lottocomboapi\venv\Lib\site-packages\wfastcgi.py
        Name: Django Handler
    
    - Click "Request Restrictions" >> Uncheck "Invoke handler only if request is mapped to"
    
    - Click OK >> Do you want to create a FastCGI Application for this Executable? >> Click Yes

    - Open "FastCGI Settings" in Computer/Username eg) My-PC\az09
    
    - Double click one created from above step eg) full path: C:\Developments\python-lotto-combo-api\lottocomboapi\venv\Scripts\python.exe
    
    - Enviorment Variables >> Click "Collection"
    
    - Enter "PYTHONPATH", "WSGI_HANDLER", "DJANGO_SETTINGS_MODULE"
        eg)
        PYTHONPATH: C:\Developments\python-lotto-combo-api\lottocomboapi
        WSGI_HANDLER: django.core.wsgi.get_wsgi_application()
        DJANGO_SETTINGS_MODULE: lottocomboapi.settings

    - Check if web.config is created on root folder of api, where manage.py exists
        eg)
        <?xml version="1.0" encoding="UTF-8"?>
        <configuration>
            <system.webServer>
                <handlers>
                    <add name="Django Handler" path="*" verb="*" modules="FastCgiModule" scriptProcessor="C:\Developments\python-lotto-combo-api\lottocomboapi\venv\Scripts\python.exe|C:\Developments\python-lotto-combo-api\lottocomboapi\venv\Lib\site-packages\wfastcgi.py" resourceType="Unspecified" />
                </handlers>
            </system.webServer>
        </configuration>
    
    - Click Restart then Click Browse Website eg) Browse 192.168.1.100:8200(http)
    
    - Troubleshooting
        A. DisallowedHost at /
            -> Add a new host to ALLOWED_HOSTS in settings.py
                eg) ALLOWED_HOSTS = ['192.168.1.100', 'localhost']

        B. Layout broken
            -> Add new Handler Mappings
                eg) 
                    Request path: *
                    Module: StaticFileModule,DefaultDocumentModule,DirectoryListingModule
                    Executable: 
                    Name: StaticFile

        C. The Web server is configured to not list the contents of this directory.
            -> Recreate a Handler Mappings
                eg) 
                    Request path: *
                    Module: FastCgiModule
                    Executable: C:\Developments\python-lotto-combo-api\lottocomboapi\venv\Scripts\python.exe|C:\Developments\python-lotto-combo-api\lottocomboapi\venv\Lib\site-packages\wfastcgi.py
                    Name: Django Handler
            -> Click No if one already exists in "FastCGI Settings" in Computer/Username
            
            -> Click Yes if need to create a new one then enter Enviroment Variables

3. Firewall rule: Port has to be open in order to call API from other computers
    - Open Windows Defender Firewall with Advanced Security
    
    - Right click on "Inbound Rules" >> New Rules 
    
    - Select "Port" >> "Specific local ports" eg) 8200
    
    - Select "Allow the connection"       
    
    - Select Domain, Private, Public
    
    - Enter Name eg) LottoCombo API Port rule

4. Deploy React App to IIS
    - npm run build

    - Right click on "Sites" >> Add Website
    
    - Enter "Site name", "Physical path", "IP Address", "Port"
        eg) 
        Site: LottoComboAPP, 
        Physical path: C:\Developments\react-lottocombo-app\build
        IP Address: Real IP Address
        Port: 8280
    
    - Open Browse Website: eg) Real IP Address:8280

5. Firewall rule: Port has to be open for public access
    - Open Windows Defender Firewall with Advanced Security
    
    - Right click on "Inbound Rules" >> New Rules 
    
    - Select "Port" >> "Specific local ports" eg) 8280
    
    - Select "Allow the connection"       
    
    - Select Domain, Private, Public
    
    - Enter Name eg) LottoCombo App Port rule