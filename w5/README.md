1. Install kubectl-ai
https://github.com/GoogleCloudPlatform/kubectl-ai

2. Prepare yaml file(s)

3. Use command:

```bash
cat prompt.txt yaml/app-secret-env.yaml | kubectl-ai --model gemini-2.5-flash --quiet > security_report.log
```

4. Check report in ``security_report.log``

Sample output:
 
security_report.log

  Here's the security analysis of the provided Kubernetes manifest:           
                                                                              
   File Name       | Title            | Short Descripti… | Fix                
  -----------------|------------------|------------------|------------------  
   app-secret-     | Secrets Exposed  | Storing          | **Volume Mount     
   env.yaml        | via Environment  | sensitive        | Secrets**:         
                   | Variables        | information like | Instead of         
                   |                  | usernames and    | exposing secrets   
                   |                  | passwords        | as environment     
                   |                  | directly in      | variables, mount   
                   |                  | environment      | them as files      
                   |                  | variables, even  | inside the         
                   |                  | if sourced from  | container using    
                   |                  | a Kubernetes     | volumeMounts.      
                   |                  | Secret, can pose | This limits        
                   |                  | a security risk. | their visibility   
                   |                  | These variables  | to the             
                   |                  | are easily       | filesystem and     
                   |                  | accessible by    | requires           
                   |                  | any process      | explicit actions   
                   |                  | running within   | to read them,      
                   |                  | the container    | making them less   
                   |                  | and could be     | prone to           
                   |                  | exposed in logs, | accidental         
                   |                  | crash dumps, or  | exposure.          
                   |                  | when inspecting  | **Example fix      
                   |                  | the container's  | for **app-         
                   |                  | environment,     | secret-env.yaml:   
                   |                  | making them      | yaml <br>          
                   |                  | vulnerable to    | apiVersion: v1     
                   |                  | accidental       | <br> kind: Pod     
                   |                  | leakage or       | <br> metadata:     
                   |                  | malicious        | <br>   name:       
                   |                  | access.          | app-secret-env     
                   |                  |                  | <br> spec: <br>    
                   |                  |                  | containers: <br>   
                   |                  |                  | - name:            
                   |                  |                  | mycontainer <br>   
                   |                  |                  | image: redis       
                   |                  |                  | <br>               
                   |                  |                  | volumeMounts:      
                   |                  |                  | <br>     - name:   
                   |                  |                  | mysecret1-volume   
                   |                  |                  | <br>               
                   |                  |                  | mountPath:         
                   |                  |                  | "/etc/secrets/my   
                   |                  |                  | secret1" <br>      
                   |                  |                  | readOnly: true     
                   |                  |                  | <br>     env:      
                   |                  |                  | <br>       #       
                   |                  |                  | Reference files    
                   |                  |                  | from the mounted   
                   |                  |                  | volume instead     
                   |                  |                  | of env variables   
                   |                  |                  | <br>       # -     
                   |                  |                  | name:              
                   |                  |                  | SECRET_USERNAME    
                   |                  |                  | <br>       #       
                   |                  |                  | valueFrom: <br>    
                   |                  |                  | #                  
                   |                  |                  | secretKeyRef:      
                   |                  |                  | <br>       #       
                   |                  |                  | name: mysecret1    
                   |                  |                  | <br>       #       
                   |                  |                  | key: username      
                   |                  |                  | <br>       # -     
                   |                  |                  | name:              
                   |                  |                  | SECRET_PASSWORD    
                   |                  |                  | <br>       #       
                   |                  |                  | valueFrom: <br>    
                   |                  |                  | #                  
                   |                  |                  | secretKeyRef:      
                   |                  |                  | <br>       #       
                   |                  |                  | name: mysecret1    
                   |                  |                  | <br>       #       
                   |                  |                  | key: password      
                   |                  |                  | <br>   volumes:    
                   |                  |                  | <br>   - name:     
                   |                  |                  | mysecret1-volume   
                   |                  |                  | <br>     secret:   
                   |                  |                  | <br>               
                   |                  |                  | secretName:        
                   |                  |                  | mysecret1 <br>     
                   |                  |                  | restartPolicy:     
                   |                  |                  | Never <br>         
                   |                  |                  | Your application   
                   |                  |                  | would then read    
                   |                  |                  | the username and   
                   |                  |                  | password from      
                   |                  |                  | /etc/secrets/mys   
                   |                  |                  | ecret1/username    
                   |                  |                  | and                
                   |                  |                  | /etc/secrets/mys   
                   |                  |                  | ecret1/password    
                   |                  |                  | respectively.      
                   |                  |                  | For certain        
                   |                  |                  | applications       
                   |                  |                  | that *require*     
                   |                  |                  | environment        
                   |                  |                  | variables,         
                   |                  |                  | consider using a   
                   |                  |                  | secret injection   
                   |                  |                  | sidecar or an      
                   |                  |                  | external secrets   
                   |                  |                  | management         
                   |                  |                  | solution.          

