import { server } from '../../../src/app.js'
import supertest from 'supertest'
const request = supertest(server)
import { randomUUID } from 'crypto'

import {
  adminPassword,
} from '../data/project.js'
import messages from '../../../src/common/messages.js'
import servicesService from '../requiredTest/servicesService.js'
import usersService from '../requiredTest/userService.js'
import projectsService from '../requiredTest/projectService.js'
import { password } from '../data/user.js'

export const serviceCreate = () => {
  describe('CREATE SERVICE', () => {
    let adminToken
    let adminId
    let serviceId
    let projectId
    let token
    let userToken
    let superAdminId
    let userId
    let service
    let adminServiceId

    beforeAll(async () => {
      const superAdminInfo = {
        username: randomUUID(),
        password: adminPassword,
        role: 'superAdmin'
      }

      superAdminId = await usersService.create(superAdminInfo)

      delete superAdminInfo.role

      token = await usersService.login(superAdminInfo)

      const adminInfo = {
        username: randomUUID(),
        password: adminPassword,
        role: 'admin'
      }

      adminId = await usersService.create(adminInfo)

      delete adminInfo.role

      adminToken = await usersService.login(adminInfo)

      const userInfo = {
        username: randomUUID(),
        password: password,
        role: 'user'
      }

      userId = await usersService.create(userInfo)

      delete userInfo.role

      userToken = await usersService.login(userInfo)

      const project = {
        name: randomUUID(),
      }

      projectId = await projectsService.create(project)
    })

    afterAll(async () => {
      await servicesService.delete(serviceId)
      await usersService.delete(superAdminId)
      await usersService.delete(userId)
      await usersService.delete(adminId)
      await projectsService.delete(projectId)
      await servicesService.delete(adminServiceId)
    })

    beforeEach(() => {
      service = {
        apis: [],
        queue: 'mockQueue',
        validation: {},
        ProjectId: projectId,
        name: 'mockName',
        type: 'direct'
      }
    })

    it('Registration completed successfully', (done) => {
      try {
        request
          .post('/api/services')
          .set('Authorization', `Bearer ${token}`)
          .send(service)
          .expect(201)

          .end(function (err, res) {
            if (err) return done(err)

            serviceId = res.body.data.message.id

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'success',
                message: expect.objectContaining({
                  id: serviceId,
                }),
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('admins can create services', (done) => {
      try {
        service.name = 'mockName2'

        request
          .post('/api/services')
          .set('Authorization', `Bearer ${adminToken}`)
          .send(service)
          .expect(201)

          .end(function (err, res) {
            if (err) return done(err)

            adminServiceId = res.body.data.message.id

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'success',
                message: expect.objectContaining({
                  id: adminServiceId,
                }),
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('service name must be string', (done) => {
      try {
        service.name = 5475844

        request
          .post('/api/services')
          .set('Authorization', `Bearer ${token}`)
          .send(service)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.SERVICE_NAME} ${messages.m.INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('service name is required', (done) => {
      try {
        delete service.name
        request
          .post('/api/services')
          .set('Authorization', `Bearer ${token}`)
          .send(service)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.SERVICE_NAME} ${messages.m.REQUIRED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('service name should not be empty', (done) => {
      try {
        service.name = ''
        request
          .post('/api/services')
          .set('Authorization', `Bearer ${token}`)
          .send(service)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.SERVICE_NAME} ${messages.m.EMPTY}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('service name is duplicate', (done) => {
      try {
        request
          .post('/api/services')
          .set('Authorization', `Bearer ${token}`)
          .send(service)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.UNIQUE_CONSTRAINT_ERROR}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('unauthorized user can not create a service', (done) => {
      try {
        request
          .post('/api/services')
          .set('Authorization', `Bearer ${userToken}`)
          .send(service)
          .expect(403)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.ACCESS_DENIED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('ProjectId is required', (done) => {
      try {
        delete service.ProjectId
        request
          .post('/api/services')
          .set('Authorization', `Bearer ${token}`)
          .send(service)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.PROJECT_ID} ${messages.m.REQUIRED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('ProjectId is not found', (done) => {
      try {
        service.name = 'prj_mock_1'
        service.ProjectId = '209f4acc-dbdd-475f-915f-fad52d42388b'

        request
          .post('/api/services')
          .set('Authorization', `Bearer ${token}`)
          .send(service)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.FOREIGN_KEY_CONSTRAINT_ERROR}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('ProjectId must be string', (done) => {
      try {
        service.ProjectId = 5475844

        request
          .post('/api/services')
          .set('Authorization', `Bearer ${token}`)
          .send(service)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.PROJECT_ID} ${messages.m.INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('ProjectId can not be empty', (done) => {
      try {
        service.ProjectId = ''

        request
          .post('/api/services')
          .set('Authorization', `Bearer ${token}`)
          .send(service)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.PROJECT_ID} ${messages.m.EMPTY}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('apis is required', (done) => {
      try {
        delete service.apis

        request
          .post('/api/services')
          .set('Authorization', `Bearer ${token}`)
          .send(service)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.APIS} ${messages.m.REQUIRED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('apis must be array', (done) => {
      try {
        service.apis = 5475844

        request
          .post('/api/services')
          .set('Authorization', `Bearer ${token}`)
          .send(service)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.APIS} ${messages.m.MUST_BE_ARRAY}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('apis can not be null', (done) => {
      try {
        service.apis = null

        request
          .post('/api/services')
          .set('Authorization', `Bearer ${token}`)
          .send(service)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.APIS} ${messages.m.MUST_BE_ARRAY}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('methods must be in array', (done) => {
      try {
        service.apis = [{
          method: 'POST'
        }]

        request
          .post('/api/services')
          .set('Authorization', `Bearer ${token}`)
          .send(service)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.METHOD} ${messages.m.MUST_BE_ARRAY}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('methods must be GET or POST', (done) => {
      try {
        service.apis = [{
          method: ['patch']
        }]

        request
          .post('/api/services')
          .set('Authorization', `Bearer ${token}`)
          .send(service)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.METHOD_VALID_FORMAT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('methods can not be empty', (done) => {
      try {
        service.apis = [{
          method: []
        }]

        request
          .post('/api/services')
          .set('Authorization', `Bearer ${token}`)
          .send(service)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.METHOD} ${messages.m.EMPTY}`
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('methods is required', (done) => {
      try {
        service.apis = [{}]

        request
          .post('/api/services')
          .set('Authorization', `Bearer ${token}`)
          .send(service)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.METHOD} ${messages.m.REQUIRED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('path is required', (done) => {
      try {
        service.apis = [{
          method: ['post']
        }]

        request
          .post('/api/services')
          .set('Authorization', `Bearer ${token}`)
          .send(service)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.PATH} ${messages.m.REQUIRED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('files name is required', (done) => {
      try {
        service.apis = [{
          method: ['post'],
          path: 'nationalCard/file',
          files: [{
            'type': 'image',
            'howGet': 'file',
            'required': true
          }]
        }]

        request
          .post('/api/services')
          .set('Authorization', `Bearer ${token}`)
          .send(service)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.FILENAME} ${messages.m.REQUIRED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('type is required', (done) => {
      try {
        service.apis = [{
          method: ['post'],
          path: 'nationalCard/file',
          files: [{
            'name': 'files',
            'howGet': 'file',
            'required': true
          }]
        }]

        request
          .post('/api/services')
          .set('Authorization', `Bearer ${token}`)
          .send(service)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.TYPE} ${messages.m.REQUIRED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('HOWGET is required', (done) => {
      try {
        service.apis = [{
          method: ['post'],
          path: 'nationalCard/file',
          files: [{
            'name': 'files',
            'type': 'image',
            'required': true
          }]
        }]

        request
          .post('/api/services')
          .set('Authorization', `Bearer ${token}`)
          .send(service)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.HOWGET} ${messages.m.REQUIRED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('files name can not be empty', (done) => {
      try {
        service.apis = [{
          method: ['post'],
          path: 'nationalCard/file',
          files: [{
            'name': '',
            'type': 'image',
            'howGet': 'file',
            'required': true
          }]
        }]

        request
          .post('/api/services')
          .set('Authorization', `Bearer ${token}`)
          .send(service)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.FILENAME} ${messages.m.EMPTY}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('files name must be string', (done) => {
      try {
        service.apis = [{
          method: ['post'],
          path: 'nationalCard/file',
          files: [{
            'name': {},
            'type': 'image',
            'howGet': 'file',
            'required': true
          }]
        }]

        request
          .post('/api/services')
          .set('Authorization', `Bearer ${token}`)
          .send(service)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.FILENAME} ${messages.m.INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('type must be one of image , voice , video', (done) => {
      try {
        service.apis = [{
          method: ['post'],
          path: 'nationalCard/file',
          files: [{
            'type': '',
            'name': 'files',
            'howGet': 'file',
            'required': true
          }]
        }]

        request
          .post('/api/services')
          .set('Authorization', `Bearer ${token}`)
          .send(service)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: messages.m.ENTRY_VALID_FILE_TYPE,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('type must be string', (done) => {
      try {
        service.apis = [{
          method: ['post'],
          path: 'nationalCard/file',
          files: [{
            'type': 3564564,
            'name': 'files',
            'howGet': 'file',
            'required': true
          }]
        }]

        request
          .post('/api/services')
          .set('Authorization', `Bearer ${token}`)
          .send(service)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: messages.m.ENTRY_VALID_FILE_TYPE,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('HOWGET can not be empty', (done) => {
      try {
        service.apis = [{
          method: ['post'],
          path: 'nationalCard/file',
          files: [{
            'howGet': '',
            'name': 'files',
            'type': 'image',
            'required': true
          }]
        }]

        request
          .post('/api/services')
          .set('Authorization', `Bearer ${token}`)
          .send(service)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.ENTRY_VALID_HOWGET}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('HOWGET must be string', (done) => {
      try {
        service.apis = [{
          method: ['post'],
          path: 'nationalCard/file',
          files: [{
            'howGet': [],
            'name': 'files',
            'type': 'image',
            'required': true
          }]
        }]

        request
          .post('/api/services')
          .set('Authorization', `Bearer ${token}`)
          .send(service)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.ENTRY_VALID_HOWGET}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('required file must be boolean', (done) => {
      try {
        service.apis = [{
          method: ['post'],
          path: 'nationalCard/file',
          files: [{
            'name': 'files',
            'howGet': 'file',
            'type': 'image',
            'required': 'tru'
          }]
        }]

        request
          .post('/api/services')
          .set('Authorization', `Bearer ${token}`)
          .send(service)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.REQUIRED_FILE} ${messages.m.INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('path must be string', (done) => {
      try {
        service.apis = [{
          method: ['post'],
          path: 195.1684525
        }]

        request
          .post('/api/services')
          .set('Authorization', `Bearer ${token}`)
          .send(service)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.PATH} ${messages.m.INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('path should not be empty', (done) => {
      try {
        service.apis = [{
          method: ['post'],
          path: ''
        }]

        request
          .post('/api/services')
          .set('Authorization', `Bearer ${token}`)
          .send(service)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.PATH} ${messages.m.EMPTY}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('queue is required', (done) => {
      try {
        delete service.queue

        request
          .post('/api/services')
          .set('Authorization', `Bearer ${token}`)
          .send(service)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.QUEUE} ${messages.m.REQUIRED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('queue must be string', (done) => {
      try {
        service.queue = 5475844

        request
          .post('/api/services')
          .set('Authorization', `Bearer ${token}`)
          .send(service)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.QUEUE} ${messages.m.INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('queue can not empty', (done) => {
      try {
        service.queue = ''

        request
          .post('/api/services')
          .set('Authorization', `Bearer ${token}`)
          .send(service)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.QUEUE} ${messages.m.EMPTY}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('validation is required', (done) => {
      try {
        delete service.validation

        request
          .post('/api/services')
          .set('Authorization', `Bearer ${token}`)
          .send(service)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.VALIDATION} ${messages.m.REQUIRED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('validation must be an object', (done) => {
      try {
        service.validation = 'validationString'

        request
          .post('/api/services')
          .set('Authorization', `Bearer ${token}`)
          .send(service)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.VALIDATION} ${messages.m.INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('validation can not be null', (done) => {
      try {
        service.validation = null

        request
          .post('/api/services')
          .set('Authorization', `Bearer ${token}`)
          .send(service)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.VALIDATION} ${messages.m.INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('incorrect field', (done) => {
      try {
        service.field = 'kjshdfehjgashfjgashjf'

        request
          .post('/api/services')
          .set('Authorization', `Bearer ${token}`)
          .send(service)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.FIELD_INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('token is expired', (done) => {
      try {
        token =
          'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6IjNhZjZhZWIyLTg3M2EtNDNiNS04ZDM0LTE0MzczZGE2ZTFiMSIsImlhdCI6MTY2NjUwNjk3MH0.o1k__1jtkL3C2Kte'

        request
          .post('/api/services')
          .set('Authorization', `Bearer ${token}`)
          .send(service)
          .expect(401)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.TOKEN_EXPIRED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('login needed', (done) => {
      try {
        request
          .post('/api/services')
          .send(service)
          .expect(401)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.LOGIN_NEEDED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })
  })
}
