�T�[�o�[4��ȏ�ō\�������personium.io�T�[�r�X���\�z���邽�߂̎菇��

---------------------------------------
�͂��߂�

���T�[�o�[�\��
  Personium���\������T�[�o�[�ɂ́A�ȉ���8��̖���������U��K�v������(��1)�B
  �EWeb				���o�[�X�v���L�V�T�[�o�[�BGlobal IP�������A�C���^�[�l�b�g�֐ڑ����Ă���K�v������B
  �EAP				�A�v���P�[�V�����T�[�o�[�BPersonium�{�̂����s����B
  �EADS_Master			���{�iMySQL�j�T�[�o�[�B�}�X�^�[�Ƃ��Đ��{��ێ�����B
  �EADS_Slave			���{�iMySQL�j�̕���(�X���[�u)�T�[�o�[�B
  �EES				ElasticSearch�����s����T�[�o�[�B
  �ENFS				NFS���ғ������邽�߂̃T�[�o�[�B
  �EBastion			���ݑ�T�[�o�[�Bansible�̎��s��A�e�T�[�o�[�ւ�SSH�ڑ��ɗp����B
  �EBackup			PIO�c�[���p�T�[�o�[(��2)�B�K�v�ɉ�����ADS_Slave��NFS�T�[�o�[����o�b�N�A�b�v���s���B

��1�F1�̃T�[�o�[�ɑ΂��A�����̖����𕡐������蓖�Ă邱�Ƃ��\�ł���B
��2�F�c�[���Ƃ���PIO�f�[�^�o�b�N�A�b�v�c�[���A�������`�F�b�N�c�[���ACell�ċA�I�폜�c�[���AElasticsearch�C���f�b�N�X���X�g�A�c�[��������Ă���B

���T�[�o�[�\����
  Personium�����s����ɂ�����A���т̂���T�[�o�[�\�����ȉ��Ɏ����B
  �E4��\��
    �T�[�o�[1�FWeb, Bastion
    �T�[�o�[2�FAP, NFS
    �T�[�o�[3�FES, ADS_Master
    �T�[�o�[4�FADS_Slave, backup


���t�@�C���\��
  /init_personium.yml			ansible-playbook�R�}���h�Ŏ��s����ׂ�yml
  /[group��].yml			group���Ƃ̕ϐ��ǂݍ��݂��s���A���s�^�X�N���܂Ƃ߂�yml
  /ansible.cfg				���s�ɕK�v�Ȑݒ肪�L�q����Ă���B�ύX�s�B

  /static_inventory/			IP�ȂǊe���ɐݒ肪�K�{�ȏ���u���t�H���_
  ��/hosts				�e�z�X�g�̐ݒ�iIP address, FQDN, group, User name, Private Key�Ȃǁj

  /group_vars/				�e��J�X�^�}�C�Y�E�`���[�j���O���s�����߂̃t�@�C����u���t�H���_
  ��/[group��].yml			group���Ƃ̃J�X�^�}�C�Y�E�`���[�j���O�ɕK�v�Ȑݒ�l���܂Ƃ߂�

  /resource/				�^�X�N�ɕK�v�ȃt�@�C���i���\�[�X/�ύX���s�v�̂��́j���܂Ƃ߂�t�H���_
    /[group��]/				group���Ƃ̃��\�[�X���i�[����

  /tasks/				�^�X�N���܂Ƃ߂�t�H���_
    /[group��]/				group���Ƃ̋�̓I�ȃ^�X�N���i�[����

  /handlers/				�n���h���[���܂Ƃ߂�t�H���_
    /[group��]/				group���Ƃ̃n���h���[���i�[����


  �����c���ɉ������ݒ肪�K�v�ƂȂ�t�@�C��

  ��[group��]�cweb, ap, nfs, es, ads_master, ads_slave, bastion�Abackup�����common��9���
  �icommon�̓T�[�o�[�̖����̖��̂ł͂Ȃ����A�����T�[�o�[�ɋ��ʂ����@�\��񋟂��邽�߂ɐݒ肷��j


---------------------------------------
�戵���Ӄt�@�C���F
�ȉ��̃t�@�C����ansible���s���Ɏ�����������邪�A�戵���ӂ̃L�[�ł���B
  /fj/dc-core/conf/salt.key
  /fj/dc-core/conf/token.key
