---
layout: post
title: Hibernate01_增删改查
category: 技术
tags: Hibernate
keywords: Hibernate、增删改查
description: 
---
    ###导包
    import java.sql.SQLException;
    import org.hibernate.HibernateException;
    import org.hibernate.Session;
    import org.springframework.orm.hibernate3.HibernateCallback;
    import org.springframework.orm.hibernate3.HibernateTemplate;
    import com.mankeep.mm.common.dao.spring.HibernateGenericDao;
    import com.mankeep.mm.core.entity.Company;
    
    
    
     * @author lin
####注意：

     * Company实体类的主键生成方式是AUTO（自增长型），是由hibernate根据底层数据库自动选择，对于oracle来说，会由hibernate来维护主键
     * @GeneratedValue(strategy=GenerationType.AUTO) 相当于 XML配置文件中的native方式
     * 如果实体类的主键生成策略使用该种方式的话，调用save（）方法保存对象的时候，会立即发送一条insert语句，并缓在session中缓存该对象
     * 
####注意： 
     * hibernatetemplate自身封装了事务，
     * hibernatetemplate就是一个模板，把我们之前的用session时开启事务，提交事务的操作都封装在一块了
     * 如果不存在事务，每次执行HibernateTemplate方法都会取得新Session，执行完数据库操作后再关闭Session。
     * 
     * 如果在集成的过程中，我们抛弃了hibernate.cfg.xml文件，直接在Spring的的配置文件中进行配置数据源的话，
     * 那你直接用HibernateTemplate里面提供的方法是可以成功操作数据的，
     * 		（hibernatetemplate中的每个方法都是一个单独的事务，相当于hibernate.cfg.xml中配置hibernate事务为自动提交）
     * 		（如果使用了spring AOP事务控制的话，hibernate会遵循aop的事务控制）
     * 如果你还是用hibernate.cfg.xml来配置数据源，在Spring的配置文件中引用hibernate.cfg.xml文件，那么你不能成功
     * 这里又要注意的是
     * 		如果你用 hibernate.cfg.xml文件配置数据源，Hibernate默认是手动提交事务，
     * 		而HibernateTemplatel提供的方法里面并没有提供事务提交，
     * 		而如果你用Spring的配置文件来配置数据源，Sping默认是自动提交的，
     * 		如果你想把Spring设置为手动提交你可以在起配置文件中进行配置：
     *
    <property name="defaultAutoCommit"
   		 false
    </property>;
      
####session的缓存:
     * 	session的接口实现中包含一系列的java集合，这些集合构成了session的缓存
     * 	所以session的缓存中存的实际上是java对象的句柄（引用），这些在session中存在引用的Java对象就是持久化对象
     * 	这也就解释了为什么一个临时对象如果被持久化对象引用，那么这个对象就会变成持久化对象（在允许级联保存的情况下，session情况缓存的时候会把该临时对象转变为持久化对象）
     * 	只有在清空缓存的时候，这些引用才会被清空，这实现了对象从持久化状态到其它状态的转变
     * 
####脏对象:
     * 	当一个持久化对象的属性与数据库中的属性不一致时，该对象就被称为脏对象
     * 		
     * 如何判断脏对象
     * 当一个实体对象被加载到session中时，Session回为这个实体对象的属性赋值一份快照
     * 在清理session缓存时，会将实体对象与之前的快照进行对比检查（脏检查）
     * 如果发现有属性不一致，则执行update语句，同步更新数据库
     * 
####session的缓存何时被清空:
     * 		1、事务提交的时候
     * 		2、在查询前，对象的属性发生了改变（脏对象），则会清空session（同步持久化对象）
     * 		3、手动flush的时候
    
####代码：
    
    	public class HibernateTestDao extends HibernateGenericDao{
    
    	//这里我们在spring的配置 了service层的事务 test*
    	public void testSave(){
    		
    		/**
    		 * hibernate中的对象具有三种状态
    		 * 1、临时状态通过new语句创建，不存在session缓存中的对象
    		 * 2、持久化状态存在session缓存中的对象，具有持久化标识，数据库中有与之对应的对象（或在清理缓存之后有与之对应的数据库对象）
    		 * 3、游离状态	已经被持久化，但是不存在session缓存中了，与临时对象唯一的区别是，具有（能与数据库中对象对应的）持久化标识（前提是没有其他程序修改这条记录）
    		 * 4、删除状态 	不在session的缓存中，并且session已经计划将其从数据库中删除的对象
    		 * 
    		 * Hibernate中对象的状态是针对某个session而言的，Hibernate保证在同一个Session实例中数据库中的每一条记录对应唯一的持久化对象
    		 * 应尽量避免同一个java对象同时被多个session对象关联，这样在每个session清理缓存的时候，相同的SQL会被执行多次。
    		 */
    		Company company = new Company();//new出来的一个对象，未与session关联，属于临时对象
    		company.setCompanyseq(3L);
    		company.setCompanyName("修该地址service");
    		/**
    		 *  
    		 * 把一个临时对象转化为持久化对象
    		 * 注意：如果实体类的主键增长策略是native（即hibernate根据底层数据库选择），相当于GenerationType.AUTO
    		 * 如果主键是这种类型,则通过save()进行持久化的时候会立即发送insert语句，并加入session缓存（如果是oracle数据库，则不会）
    		 * native（XML）的主键生成策略，是根据底层数据库选择生成主键的方式，所以对于mysql这种支持主键自增长的，必须执行insert语句才会得到持久化id
    		 * 而对于oracle则只需要执行select语句从sequence中查出主键id即可，
    		 * 所以当主键的生成策略是native（同GenerationType.AUTO），hibernate进行save()操作时，
    		 * 对于oracle这种数据库不会立即执行insert语句（只会立即发送select查询sequence），--个人进行实际验证过
    		 * 对于mysql这种支持子增长的，会立即发送insert语句（目的是为了得到持久化id）--未进行实际验证
    		 * 
    		 * ========================================save（）方法 start=====================================
    		 * Session的save（）方法使一个临时对象转变为持久化对象
    		 * 
    		 * save（）方法，并不会立即执行SQL insert语句,但是会立即为持久化对象的id赋值
    		 * 而是在Session清理缓存的时候执行insert语句
    		 * 在save（）后，又修改了持久化对象的属性，则会在清理缓存的时候，额外执行update语句（多次修改，只执行一次）
    		 * 尽量在最后执行save方法（对象的属性不在进行修改），这样可以减少操纵数据库的次数
    		 * 
    		 * 当一个对象处于持久化状态时，不允许随意修改它的持久化id//会报异常 ...altered from x1 to x2
    		 * 		注意：在使用代理主键的场合，无论对象是处于持久化、临时、游离还是删除状态，都不应该修改它的主键，
    		 * 		所以比较安全的做饭就是在定义持久化类时，就将setId设置为private类型
    		 * 
    		 * save方法是用来持久化一个临时对象的。在程序中不应该把持久化对象或游离对象传递给save方法（这是多余的）
    		 * 		对于一个持久化对象的操作，在清理session的时候都会同步到数据库，对其save是多余的
    		 * 		将一个游离对象再次持久化，它会得到一个新的持久化id，但这很可能造成数据的重复（数据库里存在一条除id外，与之都相同的数据）
    		 * 
    		 * ========================================save（）方法  end=====================================
    		 * 
    		 */
    		
    		
    		company.setCompanyseq(100L);//使用代理主键时，设置对象的持久化id是无效的（最好将主键的set方法设置为private）
    		this.getHibernateTemplate().save(company); //如果用的是native（XML配置）的主键生成方式，则会立即执行insert语句
    		company.setCompanyName("持久化后修改service11");  
    		company.setCompanyName("持久化后修改service22"); 
    		System.out.println("事务提交之前（打印该句后才发送insert和update）");
    	}
    	
    	/**
    	 * ======================================persist()方法  start=====================================
    	 * persist()方法也会持久化一个游离对象
    	 * 与save（）方法不同的是，persist方法不保证立即为一个持久化对象的id赋值，而有可能在session清理缓存的时候才为其赋值
    	 * 此外，如果在事务以外调用persist方法，那么该方法不会计划执行SQL insert语句，这可以提高负责长时间运行事务的程序的健壮性。（不太明白）
    	 * 而对于save方法，不论是在事务内，还是事务外，都会计划执行SQL insert语句
    	 * 
    	 * 对于persist方法，搞不太明白到底怎么用。。。。暂不深究
    	 * 
    	 *  ======================================persist()方法  end =====================================
    	 */
    	
    	/**
    	 * ======================================load()和get()方法  start =====================================
    	 * Session的get和load方法都能根据给定的持久化标识OID，从数据库中加载一个持久化对象，他们的区别在于
    	 * 1、当数据库中不存在与OID对应的记录时：
    	 * 		get：返回空
    	 * 		load:抛异常org.hibernate.ObjectNotFoundException
    	 * 2、检索策略不同：默认情况下，所有的持久化类都采用延迟检索策略
    	 * 		get：忽略延迟加载的配置，总是采取立即检索策略
    	 * 		load：默认采用延迟检索策略，除非修改配置为立即检索（lazy="false"）
    	 * load和get方法的使用场合：
    	 * 		如果加载一个对象的目的是为了访问它的属性，则推荐使用get
    	 * 		如果加载一个对象的目的是为了删除它，或者建立与其它对象的关系，则推荐使用load（只有真正访问它的属性的时候，才会去数据库加载数据，否则不会）
    	 * 
    	 * 注意：get和load加载得到的对象都是持久化对象
    	 * 
    	 * ======================================load()和get()方法  end =====================================
    	 */
    	@SuppressWarnings("unchecked")
    	public void testGet(){
    		
    		this.getHibernateTemplate().execute(new HibernateCallback() {
    			@Override
    			public Object doInHibernate(Session session) throws HibernateException,
    					SQLException {
    				session.get(Company.class, 1L);  //采用立即检索的策略
    				System.out.println("事务提交前：get");
    				return null;
    			}
    		});
    		
    	}
    	
    	@SuppressWarnings("unchecked")
    	public void testLoad(){
    		Company company = this.getHibernateTemplate().load(Company.class, 1L);
    		System.out.println(company);//如果我们不访问改持久化对象的属性，那么它不会去真正的从数据空中加载数据
    		
    		/*this.getHibernateTemplate().execute(new HibernateCallback() {
    			@Override
    			public Object doInHibernate(Session session) throws HibernateException,
    					SQLException {
    				Company company = (Company) session.load(Company.class, 1L);
    				System.out.println("事务提交前：load");
    				System.out.println(company);//只有真正访问的时候才加载
    				return null;
    			}
    		});*/
    	}
    	
    	/**
    	 * ======================================update方法  start =====================================
    	 * update方法可以将一个游离对象转化为持久化对象，并且会计划执行一条update语句
    	 * session只有在清理缓存的时候才会执行update语句，并且就是程序中多次修改了一个持久化对象的属性，在清理缓存时也只执行一次update
    	 * 
    	 * 注意，只要用update使一个游离对象被session关联，则即使没有修改该对象的属性，在session清理缓存的时候也会执行由update方法计划执行的update语句
    	 * 
    	 * 如果希望session仅当修改了持久化对象的属性时，才执行update语句，可在映射文件中这样修改
    	 * <class name="" table="" select-before-update="true" >
    	 * 如果做此修改，session清理缓存时会先发送一条select语句，查询数据库对象并与要持久化的游离对象进行比较，只有不一致的时候才执行update语句
    	 * 
    	 * 当update对象去关联一个游离对象时，如果同一个session中已存在相同持久化标识（OID）的对象，则会报异常：org.hibernate.NonUniqueObjectException
    	 * 此外，当update去关联一个游离对象时，当数据库中没有对应的记录，也会报异常
    	 * ======================================update方法 end== =====================================
    	 */
    	@SuppressWarnings("unchecked")
    	public void testUpdate(){
    		HibernateTemplate hitemplate = this.getHibernateTemplate();
    		Company company1 = hitemplate.get(Company.class, 1L);
    		hitemplate.evict(company1);
    		Company company2 = hitemplate.get(Company.class, 1L);
    		hitemplate.update(company1);//如果是在同一个事务中（即hibernate用的是同一个session，也就是在同一个session中）
    	}
    	
    	/**
    	 * ======================================saveOrUpdate方法 start== =====================================
    	 * saveOrUpdate同时包含了save与update方法的功能，
    	 * 如果传入的是临时对象，则调用save方法
    	 * 如果传入的是游离对象，则调用update方法
    	 * 如果传入的是持久化对象则直接返回
    	 * 显然saveOrUpdate更加灵活，但它需要额外判断对象是否已存在。
    	 * 
    	 * ======================================saveOrUpdate方法 end== =====================================
    	 */
    	public void testSaveOrUpdate(){
    		HibernateTemplate hitemplate = this.getHibernateTemplate();
    		Company company = hitemplate.get(Company.class, 1L);//得到一个持久化对象
    		company.setCompanyName("刘宝林哈哈");
    		Company company2 = new Company();
    		company2.setCompanyName("临时对象");
    	//		hitemplate.save(company);//无意义的
    	//		hitemplate.saveOrUpdate(company);//判读是持久化对象会
    	//		hitemplate.update(company2);//会报错
    		hitemplate.saveOrUpdate(company2);
    	}
    	/**
    	 *  ======================================delete方法 start=======================================
    	 *  delete方法用于从数据库中删除一个java对象，既可以删除持久化对象，也可以删除游离对象
    	 *  1、如果传入的参数是游离对象，那么删除前会先使游离对象与当前session关联，使它变为持久化对象
    	 *  2、如果是持久化对象则忽略第一步
    	 *  
    	 *  session只有在清理缓存的时候才会执行delete语句
    	 *  被删除对象是无用对象，程序中不应该继续使用被删除对象
    	 *  被删除对象与临时对象的比较：
    	 *  	1、都不存在于session中
    	 *  	2、被删除对象在数据库中不存在对应的记录（程序会计划执行delete语句，在session清理缓存后执行delete语句，删除相应记录）
    	 *  
    	 *  session的delete方法一次只能删除一个对象
    	 *  
    	 *   =====================================delete方法 end  =======================================
    	 */
    	public void testDelete(){
    		HibernateTemplate hitemplate = this.getHibernateTemplate();
    		Company company = hitemplate.load(Company.class, 43L);
    		hitemplate.delete(company);
    		System.out.println(company);
    	}
    	
    	
    	
    }
    
