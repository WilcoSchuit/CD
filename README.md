# Components:

  1. Secrets
  2. Deploy keys
  3. Workflow

# Problems:
  1. App deployed on VPS after pytest failed:
  
    Solution:
      Added the following job to test&deploy.yml:
  
      deploy:
        needs: run-tests #if run-tests succeeds, start deploy
        runs-on: ubuntu-latest
  
      This job will only start if all the steps in run-tests have succeeded.
  
  2. Unable to clone repository from GitHub on VPS if one already exists:
  
    Solution:
      Added the following line to script in test&deploy.yml: 
        rm -r assignment_cd #remove previous repository if it exists
   
      This will remove a previous repository if one already exists on VPS.
   
  3. Unable to update app:
 
    Solution:
      Added the following lines to script in test&deploy.yml:
        pkill gunicorn #stop gunicorn from running to reset
        systemctl restart nginx #restart nginx
        systemctl enable --now cd #enable cd.service
        
      This stops gunicorn from running, restarts nginx en enables cd.service. These lines combined will show the updated app in the browser.
      
      
