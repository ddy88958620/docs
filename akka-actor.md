## Akka.io��Scala&Java Actor ģ�Ϳ�

#### Remote
+ node1����demo1.conf:

		akka {
 		 loglevel = "ERROR"
      actor {
        provider = "akka.remote.RemoteActorRefProvider"
      }
      remote {
        enabled-transports = ["akka.remote.netty.tcp"]
        netty.tcp {
          hostname = "127.0.0.1"
          port = 2552	
        }
      }
    }

+ node2����demo2.conf��

    akka {
      loglevel = "ERROR"
      actor {
        provider = "akka.remote.RemoteActorRefProvider"
        deployment {         
          /hiActor {
            remote = "akka.tcp://actorSystemName@127.0.0.1:2552"
          }
        }
      }
    }

* akka.remote.netty.tcp.portΪ0ʱΪ����˿�

+ `system`������`val system = ActorSystem("actorSystemName",ConfigFactory.load("configName"));`
����`configName`Ϊ�����ļ�ǰ׺��������`demo1.conf`��`configName`Ϊ`demo1`��

+ ʹ��Զ�̽ڵ��Actor��

1.Lookup������Զ�̽ڵ��Ѵ����õ�Actor��ʹ��`actorSelection(path)`��
2.Creation������Actor��Զ�̽ڵ㣬ʹ��`actorOf(Props(...), actorName`��


+ Lookup:
���磺

    val selection = context.actorSelection("akka.tcp://actorSystemName@10.0.0.1:2552/user/actorName")

* ·��ģʽ��`akka.<protocol>://<actor system>@<hostname>:<port>/<actor path>`�����磺

    node1����һ��Actor:`val system = ActorSystem("demoActorSystem"); val hi = system.actorOf(Props[HiActor],"hiActor")`��
    ����path��`akka://demoActorSystem/user/hiActor`.

    ���node2Ҫ�������Actor����path:`akka.tcp://demoActorSystem@node1:port/user/hiActor`����ȷ��protocl��ip��port��Ϣ��
    `val hiActor = context.actorSelection("akka.tcp://demoActorSystem@node1:port/user/hiActor")`


+ Creation��

    val system = ActorSystem("demo1ActorSystem",ConfigFactory.load("demo2"))
    val hiActor = system.actorOf(Props[HiActor],"hiActor") //����name��������deployment�е���Ҫһ�£�

