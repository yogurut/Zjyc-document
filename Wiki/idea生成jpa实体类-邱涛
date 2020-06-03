通过idea生成数据库表对应的Jpa实体类操作步骤：

一、第一次使用需要先配置数据库信息，通过以下2种方式都可以打开配置页面

![](https://github.com/SN1997/Zjyc-document/blob/master/picture/qt-20200603001.png)

二、添加mysql

![](https://github.com/SN1997/Zjyc-document/blob/master/picture/qt-20200603002.png)

三、填写数据库信息，第一次需要点击左下角有个红色的按钮，下载一个插件

![](https://github.com/SN1997/Zjyc-document/blob/master/picture/qt-20200603003.png)

四、数据库连接成功

![](https://github.com/SN1997/Zjyc-document/blob/master/picture/qt-20200603005.png)

五、通过以下步骤，配置生成Jpa实体类的模板信息

![](https://github.com/SN1997/Zjyc-document/blob/master/picture/qt-20200603005.png)

六、直接复制以下模板覆盖掉原本的。  ps：这个模板是我自己改的，适用于咱们大对数项目，如有有不同，可以根据需要修改


import com.intellij.database.model.DasTable
import com.intellij.database.model.ObjectKind
import com.intellij.database.util.Case
import com.intellij.database.util.DasUtil

import java.text.SimpleDateFormat

/*
 * Available context bindings:
 *   SELECTION   Iterable<DasObject>
 *   PROJECT     project
 *   FILES       files helper
 */
packageName = ""
typeMapping = [
        (~/(?i)bigint/)                             : "Long",
        (~/(?i)tinyint|smallint|mediumint|int/)      : "Integer",
        (~/(?i)bool|bit/)                        : "Boolean",
        (~/(?i)float|double|decimal|real/)       : "BigDecimal",
        (~/(?i)datetime|timestamp|date|time/)    : "Date",
        (~/(?i)blob|binary|bfile|clob|raw|image/): "InputStream",
        (~/(?i)/)                                : "String"
]


FILES.chooseDirectoryAndSave("Choose directory", "Choose where to store generated files") { dir ->
  SELECTION.filter { it instanceof DasTable && it.getKind() == ObjectKind.TABLE }.each { generate(it, dir) }
}

def generate(table, dir) {
  def className = javaClassName(table.getName(), true)
  def fields = calcFields(table)
  packageName = getPackageName(dir)
  PrintWriter printWriter = new PrintWriter(new OutputStreamWriter(new FileOutputStream(new File(dir, className + ".java")), "UTF-8"))
  printWriter.withPrintWriter { out -> generate(out, className, fields, table) }

//    new File(dir, className + ".java").withPrintWriter { out -> generate(out, className, fields,table) }
}

// 获取包所在文件夹路径
def getPackageName(dir) {
  return dir.toString().replaceAll("\\\\", ".").replaceAll("/", ".").replaceAll("^.*src(\\.main\\.java\\.)?", "") + ";"
}

def generate(out, className, fields, table) {
  out.println "package $packageName"
  out.println ""
  out.println "import javax.persistence.*;"
  out.println "import java.io.Serializable;"
  Set types = new HashSet()

  fields.each() {
    types.add(it.type)
  }

  if (types.contains("Date")) {
    out.println "import java.util.Date;"
  }

  if (types.contains("BigDecimal")) {
    out.println "import java.math.BigDecimal;"
  }

  if (types.contains("InputStream")) {
    out.println "import java.io.InputStream;"
  }
  out.println ""
  out.println "/**\n" +
          " * @Description  \n" +
          " * @Author  qiutao\n" + //1. 修改idea为自己名字
          " * @Date " + new SimpleDateFormat("yyyy-MM-dd HH:mm:ss").format(new Date()) + " \n" +
          " */"
  out.println ""
  out.println "@Entity"
  out.println "@Table (name = \"" + table.getName() + "\" , schema = \"\")" //2. schema = \"后面添加自己的表空间名称(mysql可以不添加, 不用这个schema属性也行)
  out.println "@NamedQuery (name = \"" + className + ".findAll\" , query = \"SELECT b FROM " + className + " b\")"
  out.println "public class $className implements Serializable {"
  out.println ""
  out.println genSerialID()
  fields.each() {
    out.println ""
    // 输出注释
    if (isNotEmpty(it.commoent)) {
      out.println "\t/**"
      out.println "\t * ${it.commoent.toString()}"
      out.println "\t */"
    }

    if ((it.annos+"").indexOf("[@Id]") >= 0) out.println "\t@Id"

    if (it.annos != "") out.println "   ${it.annos.replace("[@Id]", "")}"


    // 输出成员变量
    out.println "\tprivate ${it.type} ${it.name};"
  }

  // 输出get/set方法
  fields.each() {
    out.println ""
    out.println "\tpublic ${it.type} get${it.name.capitalize()}() {"
    out.println "\t\treturn this.${it.name};"
    out.println "\t}"
    out.println ""

    out.println "\tpublic void set${it.name.capitalize()}(${it.type} ${it.name}) {"
    out.println "\t\tthis.${it.name} = ${it.name};"
    out.println "\t}"
  }

  // 输出toString方法
  out.println ""
  out.println "\t@Override"
  out.println "\tpublic String toString() {"
  out.println "\t\treturn \"{\" +"
  fields.each() {
    out.println "\t\t\t\t\t\"${it.name}='\" + ${it.name} + '\\'' +"
  }
  out.println "\t\t\t\t'}';"
  out.println "\t}"

  out.println ""
  out.println "}"
}

def calcFields(table) {
  DasUtil.getColumns(table).reduce([]) { fields, col ->
    def spec = Case.LOWER.apply(col.getDataType().getSpecification())

    def typeStr = typeMapping.find { p, t -> p.matcher(spec).find() }.value
    def comm = [
            colName : col.getName(),
            name    : javaName(col.getName(), false),
            type    : typeStr,
            commoent: col.getComment(),
            annos   : "\t@Column(name = \"" + col.getName() + "\")"]
    if ("id".equals(Case.LOWER.apply(col.getName()))) {
      comm.annos += ["@Id"]
      comm.annos += "\n\t@GeneratedValue(strategy = GenerationType.IDENTITY)"
    }
    if ("version".equals(Case.LOWER.apply(col.getName()))) {
      comm.annos += "\n\t@Version"
    }
    if ("Date".equals(typeStr)) {
      comm.annos += "\n\t@Temporal(TemporalType.TIMESTAMP)"
    }

    fields += [comm]
  }
}

// 这里是处理数据库表前缀的方法，这里处理的是t_xxx命名的表
// 已经修改为使用javaName, 如果有需要可以在def className = javaName(table.getName(), true)中修改为javaClassName
// 处理类名（这里是因为我的表都是以t_命名的，所以需要处理去掉生成类名时的开头的T，
// 如果你不需要去掉表的前缀，那么请查找用到了 javaClassName这个方法的地方修改为 javaName 即可）
def javaClassName(str, capitalize) {
  def s = com.intellij.psi.codeStyle.NameUtil.splitNameIntoWords(str)
          .collect { Case.LOWER.apply(it).capitalize() }
          .join("")
          .replaceAll(/[^\p{javaJavaIdentifierPart}[_]]/, "_")
  // 去除开头的T  http://developer.51cto.com/art/200906/129168.htm
  s = s[1..s.size() - 1]
  capitalize || s.length() == 1 ? s : Case.LOWER.apply(s[0]) + s[1..-1]
}

def javaName(str, capitalize) {
//    def s = str.split(/(?<=[^\p{IsLetter}])/).collect { Case.LOWER.apply(it).capitalize() }
//            .join("").replaceAll(/[^\p{javaJavaIdentifierPart}]/, "_")
//    capitalize || s.length() == 1? s : Case.LOWER.apply(s[0]) + s[1..-1]
  def s = com.intellij.psi.codeStyle.NameUtil.splitNameIntoWords(str)
          .collect { Case.LOWER.apply(it).capitalize() }
          .join("")
          .replaceAll(/[^\p{javaJavaIdentifierPart}[_]]/, "_")
  capitalize || s.length() == 1 ? s : Case.LOWER.apply(s[0]) + s[1..-1]
}

def isNotEmpty(content) {
  return content != null && content.toString().trim().length() > 0
}

static String changeStyle(String str, boolean toCamel) {
  if (!str || str.size() <= 1)
    return str

  if (toCamel) {
    String r = str.toLowerCase().split('_').collect { cc -> Case.LOWER.apply(cc).capitalize() }.join('')
    return r[0].toLowerCase() + r[1..-1]
  } else {
    str = str[0].toLowerCase() + str[1..-1]
    return str.collect { cc -> ((char) cc).isUpperCase() ? '_' + cc.toLowerCase() : cc }.join('')
  }
}

//生成序列化的serialVersionUID
static String genSerialID() {
  return "\tprivate static final long serialVersionUID =  " + Math.abs(new Random().nextLong()) + "L;"
}

七、选择生成路径，开始生成实体类

![](https://github.com/SN1997/Zjyc-document/blob/master/picture/qt-20200603006.png)

![](https://github.com/SN1997/Zjyc-document/blob/master/picture/qt-20200603007.png)

八、结果展示

package com.zjyc.scm.gov.entity;

import javax.persistence.*;
import java.io.Serializable;
import java.util.Date;
import java.math.BigDecimal;

/**
 * @Description  
 * @Author  qiutao
 * @Date 2020-06-03 17:08:37 
 */

@Entity
@Table (name = "t_gov_fact_apply" , schema = "")
@NamedQuery (name = "GovFactApply.findAll" , query = "SELECT b FROM GovFactApply b")
public class GovFactApply implements Serializable {

	private static final long serialVersionUID =  3293967171659979508L;

	/**
	 * ID
	 */
	@Id
   	@Column(name = "ID")
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private Long id;

	/**
	 * 业务ID
	 */
   	@Column(name = "bid")
	private String bid;

	/**
	 * 预申请ID t_gov_fact_exp_apply
	 */
   	@Column(name = "EXP_APPLY_ID")
	private Long expApplyId;

	/**
	 * 预申请编号
	 */
   	@Column(name = "EXP_APPLY_NO")
	private String expApplyNo;

	/**
	 * 招标方
	 */
   	@Column(name = "EXP_APPLY_TENDEREE")
	private String expApplyTenderee;

	/**
	 * 融资渠道
	 */
   	@Column(name = "EXP_APPLY_CHANNEL")
	private Long expApplyChannel;

	/**
	 * 申请编号
	 */
   	@Column(name = "APPLY_NO")
	private String applyNo;

	/**
	 * 申请企业：T_CUST_COMP
	 */
   	@Column(name = "APPLY_ID")
	private Long applyId;

	/**
	 * 申请企业名称
	 */
   	@Column(name = "APPLY_NAME")
	private String applyName;

	/**
	 * 申请状态：枚举- govApplyStatusEnum
	 */
   	@Column(name = "APPLY_STATUS")
	private Integer applyStatus;

	/**
	 * 融资金额
	 */
   	@Column(name = "APPLY_AMT")
	private BigDecimal applyAmt;

	/**
	 * 入账账户开户行
	 */
   	@Column(name = "BANK_NAME")
	private String bankName;

	/**
	 * 入账账号
	 */
   	@Column(name = "ACCT_NO")
	private String acctNo;

	/**
	 * 用途
	 */
   	@Column(name = "REMARK")
	private String remark;

	/**
	 * 法人姓名
	 */
   	@Column(name = "LEGAL_NAME")
	private String legalName;

	/**
	 * 法人证件类型
	 */
   	@Column(name = "LEGAL_ID_TYPE")
	private String legalIdType;

	/**
	 * 法人证件号码
	 */
   	@Column(name = "LEGAL_ID_CARD")
	private String legalIdCard;

	/**
	 * 法人联系电话
	 */
   	@Column(name = "LEGAL_PHONE")
	private String legalPhone;

	/**
	 * 法人身份证地址
	 */
   	@Column(name = "LEGAL_ID_ADDRESS")
	private String legalIdAddress;

	/**
	 * 实际控制人姓名
	 */
   	@Column(name = "CONTROL_NAME")
	private String controlName;

	/**
	 * 实际控制人证件类型
	 */
   	@Column(name = "CONTROL_ID_TYPE")
	private String controlIdType;

	/**
	 * 实际控制人证件号码
	 */
   	@Column(name = "CONTROL_ID_CARD")
	private String controlIdCard;

	/**
	 * 实际控制人联系电话
	 */
   	@Column(name = "CONTROL_PHONE")
	private String controlPhone;

	/**
	 * 实际控制人身份证地址
	 */
   	@Column(name = "CONTROL_ID_ADDRESS")
	private String controlIdAddress;

	/**
	 * 创建人（申请人）
	 */
   	@Column(name = "CREATER")
	private String creater;

	/**
	 * 创建人ID（申请人ID）
	 */
   	@Column(name = "CREATE_ID")
	private Long createId;

	/**
	 * 创建人手机（申请人手机）
	 */
   	@Column(name = "CREATE_MOB")
	private String createMob;

	/**
	 * 创建时间（申请时间）
	 */
   	@Column(name = "CREATE_TIME")
	@Temporal(TemporalType.TIMESTAMP)
	private Date createTime;

	/**
	 * 修改人
	 */
   	@Column(name = "MODIFIER")
	private String modifier;

	/**
	 * 修改人ID
	 */
   	@Column(name = "MODIFY_ID")
	private Long modifyId;

	/**
	 * 修改人手机
	 */
   	@Column(name = "MODIFY_MOB")
	private String modifyMob;

	/**
	 * 最后修改时间
	 */
   	@Column(name = "MODIFY_TIME")
	@Temporal(TemporalType.TIMESTAMP)
	private Date modifyTime;

	/**
	 * 状态（0:正常-1:禁用-2:删除）
	 */
   	@Column(name = "STATUS")
	private Integer status;

	/**
	 * 版本（Default 1）
	 */
   	@Column(name = "VERSION")
	@Version
	private Integer version;

	public Long getId() {
		return this.id;
	}

	public void setId(Long id) {
		this.id = id;
	}

	public String getBid() {
		return this.bid;
	}

	public void setBid(String bid) {
		this.bid = bid;
	}

	public Long getExpApplyId() {
		return this.expApplyId;
	}

	public void setExpApplyId(Long expApplyId) {
		this.expApplyId = expApplyId;
	}

	public String getExpApplyNo() {
		return this.expApplyNo;
	}

	public void setExpApplyNo(String expApplyNo) {
		this.expApplyNo = expApplyNo;
	}

	public String getExpApplyTenderee() {
		return this.expApplyTenderee;
	}

	public void setExpApplyTenderee(String expApplyTenderee) {
		this.expApplyTenderee = expApplyTenderee;
	}

	public Long getExpApplyChannel() {
		return this.expApplyChannel;
	}

	public void setExpApplyChannel(Long expApplyChannel) {
		this.expApplyChannel = expApplyChannel;
	}

	public String getApplyNo() {
		return this.applyNo;
	}

	public void setApplyNo(String applyNo) {
		this.applyNo = applyNo;
	}

	public Long getApplyId() {
		return this.applyId;
	}

	public void setApplyId(Long applyId) {
		this.applyId = applyId;
	}

	public String getApplyName() {
		return this.applyName;
	}

	public void setApplyName(String applyName) {
		this.applyName = applyName;
	}

	public Integer getApplyStatus() {
		return this.applyStatus;
	}

	public void setApplyStatus(Integer applyStatus) {
		this.applyStatus = applyStatus;
	}

	public BigDecimal getApplyAmt() {
		return this.applyAmt;
	}

	public void setApplyAmt(BigDecimal applyAmt) {
		this.applyAmt = applyAmt;
	}

	public String getBankName() {
		return this.bankName;
	}

	public void setBankName(String bankName) {
		this.bankName = bankName;
	}

	public String getAcctNo() {
		return this.acctNo;
	}

	public void setAcctNo(String acctNo) {
		this.acctNo = acctNo;
	}

	public String getRemark() {
		return this.remark;
	}

	public void setRemark(String remark) {
		this.remark = remark;
	}

	public String getLegalName() {
		return this.legalName;
	}

	public void setLegalName(String legalName) {
		this.legalName = legalName;
	}

	public String getLegalIdType() {
		return this.legalIdType;
	}

	public void setLegalIdType(String legalIdType) {
		this.legalIdType = legalIdType;
	}

	public String getLegalIdCard() {
		return this.legalIdCard;
	}

	public void setLegalIdCard(String legalIdCard) {
		this.legalIdCard = legalIdCard;
	}

	public String getLegalPhone() {
		return this.legalPhone;
	}

	public void setLegalPhone(String legalPhone) {
		this.legalPhone = legalPhone;
	}

	public String getLegalIdAddress() {
		return this.legalIdAddress;
	}

	public void setLegalIdAddress(String legalIdAddress) {
		this.legalIdAddress = legalIdAddress;
	}

	public String getControlName() {
		return this.controlName;
	}

	public void setControlName(String controlName) {
		this.controlName = controlName;
	}

	public String getControlIdType() {
		return this.controlIdType;
	}

	public void setControlIdType(String controlIdType) {
		this.controlIdType = controlIdType;
	}

	public String getControlIdCard() {
		return this.controlIdCard;
	}

	public void setControlIdCard(String controlIdCard) {
		this.controlIdCard = controlIdCard;
	}

	public String getControlPhone() {
		return this.controlPhone;
	}

	public void setControlPhone(String controlPhone) {
		this.controlPhone = controlPhone;
	}

	public String getControlIdAddress() {
		return this.controlIdAddress;
	}

	public void setControlIdAddress(String controlIdAddress) {
		this.controlIdAddress = controlIdAddress;
	}

	public String getCreater() {
		return this.creater;
	}

	public void setCreater(String creater) {
		this.creater = creater;
	}

	public Long getCreateId() {
		return this.createId;
	}

	public void setCreateId(Long createId) {
		this.createId = createId;
	}

	public String getCreateMob() {
		return this.createMob;
	}

	public void setCreateMob(String createMob) {
		this.createMob = createMob;
	}

	public Date getCreateTime() {
		return this.createTime;
	}

	public void setCreateTime(Date createTime) {
		this.createTime = createTime;
	}

	public String getModifier() {
		return this.modifier;
	}

	public void setModifier(String modifier) {
		this.modifier = modifier;
	}

	public Long getModifyId() {
		return this.modifyId;
	}

	public void setModifyId(Long modifyId) {
		this.modifyId = modifyId;
	}

	public String getModifyMob() {
		return this.modifyMob;
	}

	public void setModifyMob(String modifyMob) {
		this.modifyMob = modifyMob;
	}

	public Date getModifyTime() {
		return this.modifyTime;
	}

	public void setModifyTime(Date modifyTime) {
		this.modifyTime = modifyTime;
	}

	public Integer getStatus() {
		return this.status;
	}

	public void setStatus(Integer status) {
		this.status = status;
	}

	public Integer getVersion() {
		return this.version;
	}

	public void setVersion(Integer version) {
		this.version = version;
	}

	@Override
	public String toString() {
		return "{" +
					"id='" + id + '\'' +
					"bid='" + bid + '\'' +
					"expApplyId='" + expApplyId + '\'' +
					"expApplyNo='" + expApplyNo + '\'' +
					"expApplyTenderee='" + expApplyTenderee + '\'' +
					"expApplyChannel='" + expApplyChannel + '\'' +
					"applyNo='" + applyNo + '\'' +
					"applyId='" + applyId + '\'' +
					"applyName='" + applyName + '\'' +
					"applyStatus='" + applyStatus + '\'' +
					"applyAmt='" + applyAmt + '\'' +
					"bankName='" + bankName + '\'' +
					"acctNo='" + acctNo + '\'' +
					"remark='" + remark + '\'' +
					"legalName='" + legalName + '\'' +
					"legalIdType='" + legalIdType + '\'' +
					"legalIdCard='" + legalIdCard + '\'' +
					"legalPhone='" + legalPhone + '\'' +
					"legalIdAddress='" + legalIdAddress + '\'' +
					"controlName='" + controlName + '\'' +
					"controlIdType='" + controlIdType + '\'' +
					"controlIdCard='" + controlIdCard + '\'' +
					"controlPhone='" + controlPhone + '\'' +
					"controlIdAddress='" + controlIdAddress + '\'' +
					"creater='" + creater + '\'' +
					"createId='" + createId + '\'' +
					"createMob='" + createMob + '\'' +
					"createTime='" + createTime + '\'' +
					"modifier='" + modifier + '\'' +
					"modifyId='" + modifyId + '\'' +
					"modifyMob='" + modifyMob + '\'' +
					"modifyTime='" + modifyTime + '\'' +
					"status='" + status + '\'' +
					"version='" + version + '\'' +
				'}';
	}

}
