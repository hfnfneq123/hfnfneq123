імпорт  asyncio

з  pywebio  імпортувати  start_server
від  pywebio . імпорт введення  * 
від  pywebio . вихідний  імпорт  *
від  pywebio . імпорт сесії  defer_call , інформація як session_info , run_async , run_js   

chat_msgs  = []
online_users  =  set ()

MAX_MESSAGES_COUNT  =  100

async  def  main ():
    глобальний  chat_msgs
    
    put_markdown ( "## 🧊 Доброго побажання в онлайн-чаті! \n Вихідний код даного чата укладається в 100 строк коду!" )

    msg_box  =  вихід ()
    put_scrollable ( msg_box , height = 300 , keep_bottom = True )

    nickname  =  await  input ( "Війти в чат" , обов'язково = True , placeholder = "Ваше ім'я" , validate = lambda  n : "Такий ник уже використовується!"  якщо  n  у  online_users  або  n  ==  '📢'  else  None )
    онлайн_користувачі . додати ( псевдонім )

    chat_msgs . append (( '📢' , f'` { псевдонім } ` приєднався до чату!' ))
    msg_box . append ( put_markdown ( f'📢 ` { псевдонім } ` приєднався до чату' ))

    refresh_task  =  run_async ( refresh_msg ( псевдонім , msg_box ))

    в той час як  True :
        data  =  await  input_group ( "💭 Новое сообщение" , [
            input ( placeholder = "Текст повідомлення ..." , name = "msg" ),
            дії ( name = "cmd" , buttons = [ "Відправити" , { 'label' : "Вийти з чата" , 'type' : 'cancel' }])
        ], validate  =  lambda  m : ( 'msg' , "Введіть текст повідомлення!" ) if  m [ "cmd" ] ==  "Відправити"  , а  не  m [ 'msg' ] else  None )

        якщо  дані  Немає  : _
            перерва

        msg_box . append ( put_markdown ( f"` { псевдонім } `: { дані [ 'повідомлення' ] } " ))
        chat_msgs . додати (( псевдонім , дані [ 'повідомлення' ]))

    refresh_task . закрити ()

    онлайн_користувачі . видалити ( псевдонім )
    тост ( "Вы вышли из чата!" )
    msg_box . append ( put_markdown ( f'📢 Користувач ` { псевдонім } ` покинув чат!' ))
    chat_msgs . append (( '📢' , f'Пользователь ` { псевдонім } ` покинув чат!' ))

    put_buttons ([ 'Перезайти' ], onclick = lambda  btn : run_js ( 'window.location.reload()' ))

async  def  refresh_msg ( псевдонім , msg_box ):
    глобальний  chat_msgs
    last_idx  =  len ( chat_msgs )

    в той час як  True :
        очікувати  asyncio . спати ( 1 )
        
        для  m  у  chat_msgs [ last_idx :]:
            if  m [ 0 ] !=  псевдонім : # якщо не повідомлення від поточного користувача
                msg_box . append ( put_markdown ( f"` { m [ 0 ] } `: { m [ 1 ] } " ))
        
        Термін дії # видалення минув
        якщо  len ( chat_msgs ) >  MAX_MESSAGES_COUNT :
            chat_msgs  =  chat_msgs [ len ( chat_msgs ) //  2 :]
        
        last_idx  =  len ( chat_msgs )

if  __name__  ==  "__main__" :
    start_server ( основний , debug = True , порт = 8080 , cdn = False )
