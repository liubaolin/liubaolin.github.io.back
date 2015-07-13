---
layout: post
title: Hibernate的常用检索方式
category: 技术
tags: Hibernate
keywords: Hibernate查询
description: 主要是自己写的DEMO
---

	package test.hibernatetest.dao;
	import java.sql.SQLException;
	import java.util.List;
	import org.hibernate.HibernateException;
	import org.hibernate.Query;
	import org.hibernate.Session;
	import org.hibernate.criterion.Criterion;
	import org.hibernate.criterion.DetachedCriteria;
	import org.hibernate.criterion.Restrictions;
	import org.springframework.orm.hibernate3.HibernateCallback;
	import org.springframework.orm.hibernate3.HibernateTemplate;
	
	import com.mankeep.mm.common.dao.spring.HibernateGenericDao;
	import com.mankeep.mm.core.entity.Company;
	
	/**
	 * 该类主要用于测试Hibernate的检索方式
	 * @author lin
	 *
	 */
	public class HibernateTestDao3 extends HibernateGenericDao{
		/**
		 * ================================HQL START=================================================
		 * Hibernate Query Language(HQL)
		 * 集合spring对Hibernate的集成，这里测试spring常用的HQL查询方式（hibernateTemplate）
		 */
		public void testHQL(){
			HibernateTemplate hitemplate = this.getHibernateTemplate();
			
			/**
			 * 1、find(String hql);
			 */
	//		String hql = "from Company";
	//		List<Company> companys = hitemplate.find(hql);
	//		for(Company company :companys){
	//			System.out.println(company);
	//		}
			
			
			/**
			 * 2、find(String hql , Object value); 
			 * 带传参的，支持模糊查询
			 */
	//		String hql= "from Company c where c.companyName = ?";
	//		List<Company> companys = hitemplate.find(hql, "richey");
			
	//		String hql = "from Company c where c.companyName like ?";
	//		List<Company> companys = hitemplate.find(hql, "%che%");
	//		for(Company company : companys){
	//			System.out.println(company);
	//		}
			
			
			/**
			 * 3、find(String hql, Object[] values);
			 * 多个参数查询
			 */
	//		String hql = "from Company c where c.companyName=? and c.companyAddr = ?";
	//		List<Company> companys = hitemplate.find(hql,new String[]{"richey","上海"});
	//		for(Company company : companys){
	//			System.out.println(company);
	//		}
			
			/**
			 * 4、findByExample(Object exampleEntity)
			 */
	//		Company company = new Company();
	//		company.setCompanyName("richey");
	//		company.setCompanyAddr("上海");
	//		//查找   name and addr
	//		List<Company> companys = hitemplate.findByExample(company);
	//		for(Company obj : companys){
	//			System.out.println(obj);
	//		}
			
			/**
			 * QBE(Query By Example)
			 * 5、findByExample(Object exampleEntity, int start, int  maxResults)
			 * 返回满足条件的，自start起共max个User对象（对象从0开始计数）
			 */
	//		Company company = new Company();
	//		company.setCompanyName("richey");
	//		company.setCompanyAddr("上海");
	//		//查找   name and addr
	//		List<Company> companys = hitemplate.findByExample(company,0,2);
	//		for(Company obj : companys){
	//			System.out.println(obj);
	//		}
			
			/**
			 * 6、findByNamedParam(String queryString , String paramName , Object value)
			 * 动态绑定参数查询
			 */
	//		String hql = "from Company c where c.companyName = :myName";
	//		String paramName = "myName";
	//		String value = "richey";
	//		List<Company> companys = hitemplate.findByNamedParam(hql, paramName, value);
	//		for(Company obj : companys){
	//			System.out.println(obj);
	//		}
			
			
			/**
			 *  7、findByNamedParam(String queryString , String[] paramName , Object[] value)
			 */
			String hql = "from Company c where c.companyName = :myName and c.companyAddr=:myAddr";
			String[] paramNames = new String[]{"myName","myAddr"};
			String[] values = new String[]{"richey","上海"} ;
			List<Company> companys = hitemplate.findByNamedParam(hql, paramNames, values);
			for(Company obj : companys){
				System.out.println(obj);
			}
		}
		//=====================================HQL end ============================================
		
		/**
		 * =====================================QBC start ============================================
		 * Query By Criteria(QBC)
		 * 支持在运行时动态生成查询语句
		 */
		public void testCriteria(){
			HibernateTemplate hitemplate = this.getHibernateTemplate();
			//创建一个criteria对象
			DetachedCriteria criteria=DetachedCriteria.forClass(Company.class);
			//设定查询条件，每个Criterion实例代表一个查询条件
			Criterion criteria1 = Restrictions.like("companyName", "%che%");
			Criterion criteria2 = Restrictions.eq("companyAddr", "上海");
			criteria.add(criteria1).add(criteria2);
			//进行查询
			List<Company> companys = hitemplate.findByCriteria(criteria);
			for(Company obj : companys){
				System.out.println(obj);
			}
		}
		//=====================================QBC end ============================================
	
		/**
		 * ===================================SQL start=============================================
		 * Hibernate支持本地SQL的检索方式
		 */
		public void testSql(){
			HibernateTemplate hitemplate = this.getHibernateTemplate();
			@SuppressWarnings("unchecked")
			List objs = hitemplate.execute(new HibernateCallback<List>() {
				@Override
				public List doInHibernate(Session session) throws HibernateException,
						SQLException {
					
					Query query = session.createSQLQuery("select * from mm_company_td t where t.name=:name and t.companyaddr=:addr");
					query.setString("name","richey");
					query.setString("addr", "上海");
					List list = query.list();
					return list;
				}
			});
			for(Object obj : objs){
				System.out.println(obj);
			}
		}
		
		}
