---
layout: post
title:Hibernate和Spring对Session的管理
category: 技术
tags: Hibernate
keywords: Hibernate、Spring、Session
description: 
---

	/**
	 * Hibernate中的session
	 * sessionFactory().openSession();  
	 * 		始终创建新的session
	 * sessionFactory().getCurrentSession();
	 * 		使用getCurrentSession需要设置Hibernate配置文件
	 *	  		如果要Session对象的生命周期与本地线程绑定，则配置 <property name="hibernate.current_session_context_class">thread</property>
	 *	  			当前Session对象何时创建及销毁由Hibernate来管理，Hibernate按照以下规则把session与本地线程绑定
	 *				· threadA第一次调用sessionFactory().getCurrentSession()时创建sessionA，与threadA绑定，并返回sessionA
	 *				· thraedA再次调用sessionFactory().getCurrentSession()时，始终返回sessionA
	 *				· 当thradA提交与sessionA关联的事务时，清空sessionA缓存并，关闭sessionA
	 *				· 当threadA撤销与session关联的事务时，也会关闭sessionA
	 *				· 当thraedA再次调用sessionFactory().getCurrentSession()时，会创建一个新的session，重复第一部
	 *				总结：sessionFactory().getCurrentSession()是线程绑定的，事务相关的
	 *			如果要Session对象的声明周日与JTA事务绑定，则配置 <property name="hibernate.current_session_context_class">jta</property>
	 * 			--因为getCurrentSession()要根据上下文来生成session,如果上下文存在session则不创建新的session,否则创建新的
	 * 
	 *
	 *※ getCurrentSession()与openSession()的区别？
	 * 采用getCurrentSession()创建的session会绑定到当前线程中，而采用openSession()创建的session则不会
	 * 采用getCurrentSession()创建的session在commit或rollback时会自动关闭，而采用openSession()创建的session必须手动关闭
	 * 使用getCurrentSession()需要在hibernate.cfg.xml文件中加入如下配置：
	 * 如果使用的是本地事务（jdbc事务）
	 * <property name="hibernate.current_session_context_class">thread</property>
	 * 如果使用的是全局事务（jta事务）
	 * <property name="hibernate.current_session_context_class">jta</property>
	 * 
	 * 注意：使用getCurrentSession的方式获取session，必须是在事务开启的前提下
	 * 
	 * --获取基于上下文的session
	 * 在Hibernate3.0之前，获取上下文session，要使用openSession+ThreadLocal的方式（3.0之前没有getCurrentSession）
	 * 而openSession的到的session必须关闭，即通过openSession+ThreadLocal的方式获取到的Session在使用后必须手工关闭，并且将thraedlocal的本地变量置为null
	 * 
	 * HibernateDaoSupport.getSession() --从当前线程从取得session   受spring的事务控制
	 * 这个方法本身其实返回的是与当前事务绑定的Session对象，在HibernateDaoSupport中使用，
	 * HibernateDaoSupport本身是不负责对这个Session对象进行关闭的，所以在其中有一个对应的releaseSession()方法，用于关闭Session。 
	 * 　但是一般使用Spring时，我们会采用HibernateTransactionManager进行事务管理，
	 * 把事务配置在Service层。此时，它会帮助我们关闭与当前事务绑定的Session对象，
	 * 
	 * @param hql
	 * @param values
	 * @return
	 */
	public Query creatQuery(String hql,Object... values){

		/**
		 *  HibernateDaoSupport.getSession() 会返回与当前事务绑定的session对象，
		 *  HibernateDaoSupport本身不负责对这个session对象进行关闭
		 *  但是一般使用Spring时，我们会采用HibernateTransactionManager进行事务管理，它会帮助我们关闭与当前事务绑定的Session对象
		 */
		this.getSession(); 
	
		return null;
	}




