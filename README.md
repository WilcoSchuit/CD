# Components:

  1. Deploy keys
    
      Generated a public server key on the VPS with ssh-keygen, added it to SSH keys on GitHub account and to nano .ssh/authorized_keys. Used chmod 700 .ssh/authorized_keys to           give permission to perform ssh tasks. This enables the repository to be cloned from GitHub to the server.  
    
  2. Secrets

      Added the following secrets to the repository to enable SSH access: 
      - SSH_HOST: containing the servers IP adress
      - SSH_KEY: containing the public server key
      - SSH_USERNAME: containing the username, in this case root
      - 
  3. Workflow
 
     Created a workflow containing two jobs: 
      1. run-tests: which installs the dependencies from requirements.txt and runs pytest.
      2. deploy: starts when all steps from run-tests are successfully completed, performs the following actions:
        - uses secrets to SSH into the server 
        - navigates to desired folder
        - deletes previous folder if it already exists    
        - clones repository from GitHub into folder
        - stops gunicorn
        - restarts nginx
        - enables cd.service to show the updated app in browser    


# Problems encountered

  1. App deployed on VPS even after pytest failed
  
    Solution:
      Added the following job to test&deploy.yml:
  
      deploy:
        needs: run-tests #if run-tests succeeds, start deploy
        runs-on: ubuntu-latest
  
      This job will only start if all the steps in run-tests have succeeded.
  
  2. Unable to clone repository from GitHub on VPS if one already exists
  
    Solution:
      Added the following line to script in test&deploy.yml: 
      
        rm -r assignment_cd #remove previous repository if it exists
   
      This will remove a previous repository if one already exists on VPS.
   
  3. Unable to update app
 
    Solution:
      Added the following lines to script in test&deploy.yml:
      
        pkill gunicorn #stop gunicorn from running to reset
        systemctl restart nginx #restart nginx
        systemctl enable --now cd #enable cd.service
        
      This stops gunicorn from running, restarts nginx en enables cd.service. These lines combined will show the updated app in the browser.
      
      
