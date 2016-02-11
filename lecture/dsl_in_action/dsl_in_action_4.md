# Internal DSL implementation patterns
## Filling your DSL toolbox
* DSL�̊J���̃v���͂����Ȏ������@��m���Ă���
* �O�̏͂ň������悤�ȁAinternal DSL�ɋ��ʂ���p�^�[�����o���悤�B
* Internal DSL�̕���
    * Embedded  
    DSL�̃R�[�h�S�Ă�z�ɏ��� 
        * Smart API  
	fluent interface���g��
        * Reflective metaprogramming  
	���t���N�V������p������@�B
        * Typed embeddding  
	�^��V������`�����@�B
    * Generative  
    �R���p�C�����A�������͎��s���ɃR�[�h�̈ꕔ��(�J��Ԃ������Ȃ�)��
    �����I�ɐ�������
        * �R���p�C�������^�v���O���~���O
        * ���s�����^�v���O���~���O

* ���̂悤�ȕ��ނ͂��邪�A�����̎�@�͌ʂɎg�p�����킯�ł͂Ȃ��A�g�ݍ��킳��Ďg�p�����ꍇ�������B

## Embedded DSLs : patterns in metaprogramming
* ���^�v���O���~���O�Ƃ́A�{�Ȃǂ̒�`�ɂ��΃v���O�������������߂̃v���O�������������ƁB
* �������A����ł͌���������₷���A���ۂɂ́u�R���p�C�����A���s����
���^�I�u�W�F�N�g�𐶐�����v���O���~���O�v�Ƃ�����B
* ���̏͂ł̓��^�v���O���~���O�̃e�N�j�b�N���g�p�������������B
    * �܂����^�v���O���~���O�̔\�͂������Ȃ����ꂩ��n�߂�B
    * ���ɁA���^�v���O���~���O������̂ɂӂ��킵������Ŏ�������B
* �����̎d��
    1. �T���v���̃��[�X�P�[�X��������
    2. �������x���̍\���ɕ�������
    3. �l�X�Ȏ�@��p���Ď�������

### implicit context and Smart API
* DSL�̕\���͂̎w�W�Ƃ�
    * �������[�X�P�[�X���݂ė����ł���Ȃ�A����DSL�͕\���͂��\���ɂ���B
* context�Ƃ�
    1. �ȑO�̃R�[�h�Œ�`����Ă���C���X�^���X
    2. �����I�A�������͈ÖٓI�ɐݒ肳��Ă�����s��
* �����I��context��������
    * List4.2�̂悤�ȃR�[�h���������ꍇ�A�ȉ��̂悤�ɖ����I��
    �C���X�^���X(acc)�������Ȃ���΂Ȃ�Ȃ��B  
          Account acc = new Account(number);  
          acc.addHolder(hName1);  
          acc.addAddress(addr);  
    * �������������I��acc�Ə����Ȃ��Ă͂����Ȃ��̂ŁA�ǂ݂ɂ����Ȃ�B
    * DSL�̏ꍇ�Aimplicit��context���w��ł�������ǂ��B

* Implicit��contxt���w�肷����@
    * ���t���N�V������p�������^�v���O���~���O�𗘗p���Ď������Ă���B
          class Account
              atter_reader :no, :names, :addr, :type, :email_address
              // rest as in listing 4.1
              def self.create(&block)
                  account = Account.new
              	  account.instance_dval(&block)
                  accout
              end
          end
    * instance_eval��block��n���ĕ]�����Aaccount�𐶐�����B
    * ���̃R�[�h���AReflective ���^�v���O���~���O�̗�ł���B

### Reflective metaprogramming with dynamic decorators
* ���̐߂ł́A���s�����^�v���O���~���O�̕ʂ̗��p�@���Љ��B
    ���I�ɃC���X�^���X��decorate������@���B 
  * decorator�p�^�[���͎��s���ɓ��I�ɃN���X�ɋ@�\��ǉ����邽�߂Ɏg�p����B
* Decorator in Java
  * Trade�̗��p���Ďn�߂�
  * Trade���f�R���[�g���邱�Ƃ�net settlement value(NPV)�̌v�Z���@��ς���
  * NPV�ɂ�2�̃R���|�[�l���g���܂܂��
    * gross cash value
      * �P���Ɨʂ���v�Z�ł���
    * tax��fee
      * ������A�����n��A�Ώێ���ɂ���ĕω�����
  * Listing4.5����ȉ��̂悤�ȃX�N���v�g���������Ƃ��ł���B  
          Trade t = new CommissionDecorator(
              new TaxFeeDecorator(new Trade()));
          System.out.pringln(t.value());
* Improveint the Java implementation
  * ���_
    * �\���͂ƃh���C���t�����h���[
      * java�ł͌��E
    * Trade��Decorator�ɋ������т�������
      * �ÓI�Ȍp���֌W���Ȃ����΁Adecorator�̍ė��p�������܂�
    * �f�R���[�^����ŁATrade����ɂȂ�A�ǐ����Ⴂ
      * �����𔽑΂ɂ���
* duck typing�𗘗p����΁A�ÓI�^���S���]���ɂ��āA�ė��p�\�Ȓ��ۉ����\�ł���
  * duck typing
    * Smalltalk�APython�ARuby�Ȃǂ̂������̓��I�^�t���I�u�W�F�N�g
    �w���v���O���~���O����ɓ����I�Ȍ^�t���̍�@�̂��Ƃł���B
    * �����̌���ł̓I�u�W�F�N�g�i�ϐ��̒l�j�ɉ����ł��邩��
    �I�u�W�F�N�g���̂��̂����肷��B
    * �܂�A�I�u�W�F�N�g������C���^�t�F�[�X�̂��ׂĂ�
    ���\�b�h�������Ă���Ȃ�΁A���Ƃ����̃N���X�����̃C���^�t�F�[�X��
    �錾�I�Ɏ������Ă��Ȃ��Ƃ��A�I�u�W�F�N�g�͂��̃C���^�t�F�[�X�����s����
    �������Ă���Ƃ݂Ȃ���A�Ƃ������Ƃł���B
    * ����͂܂��A�����C���^�t�F�[�X����������I�u�W�F�N�g���m���A
    ���ꂼ�ꂪ�ǂ̂悤�Ȍp���K�w�������Ă���̂��Ƃ������ƂƖ��֌W�ɁA
    ���݂Ɍ����\�ł���Ƃ����Ӗ��ł�����B
    * C++��template�̓_�b�N�E�^�C�s���O�̐ÓI�łł���
* Dynamic decorators in Ruby
  * with���\�b�h�݂̂��ǉ�����Ă���
  * Trade�̐���������O�ɁAdecorator�����悤
    * decorator��mixin�Ƃ��Ď�������Ă���
    * �܂��Adecorator�͐ÓI��Trade�N���X�ƌ��т��Ă��Ȃ�
      * duck typing��p���Ă���̂ŁA
      value���\�b�h�����������S�ẴN���X�ɑ΂���decorate���邱�Ƃ��ł���
      * super�ɂ���Đe�N���X��value�����X�ɌĂяo���悤�ɂȂ��Ă���B
  * with���\�b�h�͈�����decoraor�����A�C���X�^���X�����
    * ���̎��ɓ��I���^�v���O���~���O���g�p����Ă���
    * ���s���ɖ]�ރC���X�^���X���쐬�ł��邽�߈ȉ��̌��ʂ�����
      * �ė��p�\
      * �a����
  * ��L�̃R�[�h����ȉ��̂悤�ȃX�N���v�g���������Ƃ��ł���
          tr = Trade.new('r-123', 'a-123', 'i-123, 2000)
              .with TaxFee, Comission
          puts tr.value
  * ���s�����^�v���O���~���O�̃����b�g
    * �Ȍ��ɂȂ�
    * �\���͂�����
    * ���I�ɃC���X�^���X�𑀍�ł���
  * �f�����b�g
    * �ÓI�^���S�ł͂Ȃ��Ȃ�
    * ���s���x���x���Ȃ�

## Embedded DSLs : patterns with typed abstractions
* �^�V�X�e����p����DSL�̍쐬�ɂ���

### Higher order functions as generic abstractions
* ���̏͂ł́A���Z���i�̎�������̃h�L�������g�쐬�̗�������B
* �O���[�v�������ꂽ���|�[�g�̗�
  * �}4.7�͂���N���C�A���g�̎���񍐏��̗�ňȉ��̓��e���܂܂��
    * ����
    * ��
    * �������
    * ���z
  * �}4.8�͖����ɂ���ăO���[�v������������
  * �}4.9�͗ʂɂ���ăO���[�v������������
* ���̂悤�ɁA�l�X�Ȓl��p���ăO���[�v�������������Ƃ�����
* �������������ɂ͈ȉ���2�̕��@������
  * ���ꂼ��̃O���[�v������ʂ̊֐��Ŏ�������
  * groupBy�Ƃ����R���r�l�[�^�����A���K�֐��ŕ��ޕ��@��^����
* Scala�̊o����ׂ����@
  * case class : F#�̃��R�[�h�̂̂悤�Ȃ���
  * �Öق̌^�ϊ�
  * For-comprehensions : �ȉ��̂悤�ɏ������Ƃ��ł���
          for {
              list <- nestedList
              num <- list
          } print(num)
  * ���K�֐�
* Setting UP
  * DSL�ł͈ȉ��̂悤�ɏ�������
          activityReport groupBy(_.instrument)
          activityReport groupBy(_.quantity)
  * p.108�̃R�[�h�̐���
    1. �^�̒�`
      * �h���C���̕����^�Œ�`����̂͏d�v
        * �R�[�h�������̂��Ƃ�������Ă���邩��
        * �h���C�����[�U�ɗD����������
        * Instrument�̌^����ŕύX����Ȃǂ̕ێ炪�y������
    2. TradeQuantity��case class
    3. �Öق̌^�ϊ�
    4. �񍐏��ōł��d�v�Ȓ��ۉ����ꂽ�^
* ���ꂼ��ʁX�Ɏ���������@
* groupeBy�Ŏ���������@

### Using explicit type constraints to model domain logic 
* ���̏͂ł́A�ȉ��̂��ꂼ��̌���ɂ�����r�W�l�X���b�W�N�̐�����
�`�F�b�N���@���������
  * ���I�^�t����
    * ���s���Ɍ^�����܂�̂ŁA���s���ɐ������^���`�F�b�N���Ȃ��Ă͂Ȃ�Ȃ�
    * �����`�F�b�N�����s���ɉ��x���s���K�v������
    * ���s���̃`�F�b�N���������s���Ă��邩�P�̃e�X�g�������Ȃ��Ă͂Ȃ�Ȃ�
  * �ÓI�^�t����
    * �R���p�C�����ɐ������^���`�F�b�N�����̂ŁA
    ���s�Ƀ`�F�b�N����K�v�͂Ȃ�
