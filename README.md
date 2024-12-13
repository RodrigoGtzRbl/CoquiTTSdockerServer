# **Local TTS using docker**
______________________________________________________________________
Here you can use [Coqui TTS](https://github.com/coqui-ai/TTS) in docker
The objetive of this project is to solve these problems:
- The instance of TTS could take more than 6s
- Sintetizing more than 1 time is required for achieve the proyect objective
- The venv with all the TTS libraries could take more than 7GB

<div align='center'>
## If you have any of these problems (like me) that could be your solution
</div>
______________________________________________________________________

<div align='center'>
## Structure
</div>

The project is a venv with the python 3.13v which has:
- *client.py* as the main script of the project
- *kamehouse0-tts-server* dir; inside exists a docker compose file and another dir with the Dockerfile and another dir with files, these files are the requirements for the Dockerfile and the server script
- *shared* dir, this file is shared with the docker container making easy the audio connection between client and server

______________________________________________________________________
## How it works


The client script uses 
<div align='center'>
- *os* library to create the paths
```python	
	rootPath = os.getcwd()

	audio_directory = os.path.join(rootPath, 'shared')
	dockerComposePath = os.path.join(rootPath, 'kamehouse0-tts-server/docker-compose.yml')	
```

- *subprocess* library to start and close the docker container, also checks the status of the container waiting to be up
```python	
	startDocker = ["docker", "compose", "-f", f'{dockerComposePath}', "up", "-d"]
	subprocess.run(startDocker)	
	
	while True:
    		psDocker = ["docker", "compose", "-f", f'{dockerComposePath}', "ps"]
    		output = subprocess.run(psDocker, capture_output=True, text=True)
    		if "Up" in output.stdout:
        		break
    		else:
        		print('Waiting for the connection...')
    		time.sleep(1)
```

- Function *sintetize* with a dinamic connection, that make able to close the connection dinamically

- *While* loop checking if the conn returns any response, thats because docker could takes a few seconds to start and the TTS instance could take more than 8 seconds, these loop prevents the broken pipe, because it creates a new connection with server every loop
```python	
	while True:
    	result = sintetize('De... puta... madre...')
    	if result:
        	break
   	 else:
   	     print('Trying in 5s...')
    	    time.sleep(5)
```
</div>
______________________________________________________________________

By the moment only spanish voice is set in server script, that could be easily changed starting the docker container and use the command:
```bash
tts --list_models
```







